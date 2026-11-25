# 第6回 解答

## 演習

<!-- [tool call] の行は、表示された行をそのまま貼り付けてください。終わらなかった STEP は、空欄のままでかまいません。 -->

- STEP 1 のツールの名前：glossary_search_glossary
- STEP 2 の [tool call]：glossary_search_glossary({'query': '3レベル'})
- STEP 3 の [tool call]：fetch_fetch({'url': 'https://modelcontextprotocol.io/'})
- STEP 4 の [tool call]：aws_aws___search_documentation({'search_phrase': 'Amazon S3 バージョニング 概要', 'topics': ['general']})／aws_aws___search_documentation({'topics': ['general'], 'search_phrase': 'Using versioning in S3 buckets overview'})／glossary_search_glossary({'query': 'S3'})
- 気づき・疑問：
  - エージェントのコードは一行も変えず、app/mcp_servers.json に数行足すだけでツールが 1個 → 2個 → 7個と増え、そのまま使えた。モデルに渡るのは第5回までと同じ「名前・説明文・引数」で、ツールがどこで動いているかはモデルには見えない。
  - ツールを足すと費用が増える。入力トークンは STEP 2 で 715、STEP 3 で 2,230、STEP 4 で 15,959（モデル応答 4回）。AWS のツールは説明文が長く、その全文を毎回送り直すためと考えられる。STEP 4 では、日本語の検索語で外れて英語で検索し直したほか、答えに不要な用語集の検索（S3）も呼んでいた。ツールが増えると、使わなくてよい道具まで試すことがある。
  - fetch の説明文には「もともとインターネットにアクセスできなかったとしても、このツールでできるようになった」というモデルへの指示のような文が入っていた。他人が作ったサーバーを足すと、その説明文もモデルへの指示として入る。足す前に提供元と中身を確認する必要があると感じた。
  - 疑問：ツールが何十個にもなったとき、必要なものだけを渡す仕組み（講義の「ツール検索」）を使うと、入力トークンと不要な呼び出しはどれくらい減るのか。

## 宿題

<!-- 基礎・実装・発展のどれか1つに取り組めば完了です。 -->

- 取り組んだレベル：基礎
- やったこと：app/mcp_server.py の TODO の下に `server.tool()(tools.get_lecture_summary)` を1行書き足した。エージェント側（app/mcp_agent.py、app/mcp_servers.json）は変えていない（git status で変更は app/mcp_server.py だけと確認）。その後、`--list-tools`、`--trace "第2回では何を学びますか？"`、`uv run pytest -q` を実行した。
- 結果：
  - `--list-tools`：ツールが 7個 → 8個になり、`glossary_get_lecture_summary` が増えた（説明文「指定した回（1始まりの整数）の講義の要点を返す。「第3回では何を学ぶ？」のように、回の内容や関係を聞かれたときに使う。」、引数 `{"lecture": {"title": "Lecture", "type": "integer"}}`）。
  - `--trace`：`[tool call] glossary_get_lecture_summary({'lecture': 2})`。第2回の要点（ファインチューニングと事前学習の違い、SFT、PEFT・LoRA、QLoRA、Colab での演習）をもとに答え、最後に「関連する回：第2回」と示した。`[usage] 入力 5,942 tokens / 出力 272 tokens / モデル応答 2 回`。
  - `uv run pytest -q`：21 passed。
- 気づき・疑問：
  - サーバー側に1行足すだけで、エージェントを変えずにツールが増えて使えた。エージェントは起動するたびにサーバーへツールの一覧を問い合わせる（tools/list）ので、修正は1か所で済む。
  - 「第2回では何を学びますか？」と聞いただけで、モデルは8個の中から新しいツールを選び、「第2回」を整数の 2 に直して渡した。選ぶ手がかりは説明文と引数の型だけなので、説明文が効いていると考えられる。
  - ツールは 8個に増えたが、入力は 5,942 トークンで、演習の STEP 4（7個、15,959 トークン）より少なかった。モデル応答が 2回で済んだためと考えられる。ツールの数だけでなく、何回やりとりするかで費用が決まる。
  - 「ツールを足せば便利になる」という単純な話ではなく、説明文の書き方、ツールの数、やりとりの回数がからみ合って、選び方や費用が変わる。こうした動作を理解した上で、ハーネス（どのツールを、どんな説明で渡すか）を考える必要があると、少し理解が進んだ。