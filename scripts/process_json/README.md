## JSONファイルを処理する

このスクリプトは、JSON形式のドキュメントのファイル・ダンプを処理し、いくつかのメタデータとともにベクトル・データベースに保存するユーティリティです。また、オプションで言語モデルを用いてドキュメントに個人を特定できる情報（PII）があるかどうかをスクリーニングし、検知された場合はその情報をスキップすることも可能です。さらに、スクリプトは言語モデルを使用してドキュメントからメタデータを抽出することができます。個人情報（PII）検出関数は [`services/pii_detection`](../../services/pii_detection.py) に、メタデータ抽出関数は [`services/extract_metadata`](../../services/extract_metadata.py) に、それぞれ記載されていますので、ユースケースに応じてカスタマイズしてください。

## 使用方法

このスクリプトをターミナルから実行するには、次のフォルダに移動して、以下のコマンドを実行します：

```
python process_json.py --filepath path/to/file_dump.json --custom_metadata '{"source": "file"}' --screen_for_pii True --extract_metadata True
```

where:

- `path/to/file_dump.json` は処理するファイル・ダンプの名前または パスです。このJSONファイルの形式は、JSONオブジェクトのリストである必要があり、各オブジェクトはドキュメントを表します。JSONオブジェクトは、以下のフィールドのサブセットを持つ必要があります： `id`, `text`, `source`, `source_id`, `url`, `created_at`, そして `author` です。`text` フィールドは必須ですが、それ以外はオプションで、ドキュメントのメタデータを作成するために使用されます。`id` フィールドが指定されなかった場合、ランダムな UUID がドキュメントに対して生成されます。
- `--custom_metadata` はドキュメントのメタデータを更新するためのキーと値のペアのJSON列で、オプションです。例えば、`{"source"： "file"}` は、各ドキュメントのメタデータに値 `file` を持つ `source` フィールドを追加します。デフォルトの値は空のJSONオブジェクト(`{}`) です。
- `--screen_for_pii` は個人情報（PII）検出関数を使用するかどうかを示すためのオプションのブール型フラグです。`True`に設定すると、スクリプトは [`services/pii_detection`](../../services/pii_detection.py) モジュールの `screen_text_for_pii` 関数を使用して、言語モデルを使用してドキュメントのテキストに何らかの情報 (PII) があるかどうかをチェックします。PIIが検出された場合、スクリプトはWARNINGを表示し、そのドキュメントの処理をスキップします。デフォルト値は `False` です。
- `--extract_metadata` は言語モデルを使用してドキュメントからメタデータを抽出するかどうかを指定するオプションのブール型フラグです。`True`に設定すると、スクリプトは [`services/extract_metadata`](../../services/extract_metadata.py) モジュールの `extract_metadata_from_document` 関数を使用して、ドキュメントテキストからメタデータを取り出し、それに応じて metadata オブジェクトを更新します。デフォルト値は`False`です。

このスクリプトでは、JSONファイルを辞書のリストとしてロードし、データに順番に処理を行ってドキュメントオブジェクトを作成し、それらをバッチでデータベースに挿入・更新します。また、進捗状況やエラーメッセージ、PII検出によりスキップされたアイテムの数や内容などの情報が表示されます。

オプションとその説明についての要約を得るには、`python process_json.py -h`を使用することができます。

「[example.json](example.json)」のファイルを使用して、スクリプトをテストしてください。
