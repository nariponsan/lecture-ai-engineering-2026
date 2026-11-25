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

- 取り組んだレベル：基礎 / 実装 / 発展
- やったこと：
- 結果：
- 気づき・疑問：