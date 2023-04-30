## JSONLファイルの処理

このスクリプトは、JSONL形式で書かれたドキュメントのファイルダンプを処理して、いくつかのメタデータと共にベクトルデータベースに格納するためのユーティリティです。オプションで、言語モデルを使用して個人を特定可能な情報（PII）をスクリーニングし、検出された場合にはスキップすることもできます。さらに、言語モデルを使用してドキュメントからメタデータを抽出することもできます。使用例に合わせて [`services/pii_detection`](../../services/pii_detection.py) でPII検出関数をカスタマイズしたり、[`services/extract_metadata`](../../services/extract_metadata.py) でメタデータ抽出関数をカスタマイズすることができます。

## 使用法

ターミナルからこのスクリプトを実行するには、このフォルダに移動して次のコマンドを使用します。

```
python process_jsonl.py --filepath path/to/file_dump.jsonl --custom_metadata '{"source": "email"}' --screen_for_pii True --extract_metadata True
```

ここで：

- `path/to/file_dump.jsonl` は、処理するファイルダンプの名前またはパスです。このJSONLファイルの形式は、改行区切りのJSONファイルである必要があり、各行がドキュメントを表す有効なJSONオブジェクトである必要があります。 JSONオブジェクトには、次のフィールドのサブセットが含まれている必要があります：`id`、`text`、`source`、`source_id`、`url`、`created_at`、および`author`。 `text`フィールドは必須ですが、残りはオプションであり、メタデータを構成するために使用されます。 `id`フィールドが指定されていない場合、ドキュメントのためにランダムなUUIDが生成されます。
- `--custom_metadata`は、ドキュメントのメタデータを更新するためのオプションのキーと値のペアのJSON文字列です。 たとえば、 `{"source": "file"}` は各ドキュメントのメタデータに `source` フィールドを追加し、その値を`file`に設定します。 デフォルト値は空のJSONオブジェクト（ `{}`）です。
- `--screen_for_pii`は、PII検出関数を使用するかどうかを示すオプションのブールフラグです。 `True`に設定されている場合、スクリプトは[`services/pii_detection`](../../services/pii_detection.py)モジュールから `screen_text_for_pii`関数を使用して、言語モデルを使用してドキュメントテキストにPIIが含まれているかどうかをチェックします。 PIIが検出された場合、スクリプトは警告を表示してドキュメントをスキップします。 デフォルト値は `False`です。
- `--extract_metadata`は、言語モデルを使用してドキュメントからメタデータを抽出し、メタデータオブジェクトを更新するかどうかを示すオプションのブールフラグです。 `True`に設定されている場合、スクリプトは[`services/extract_metadata`](../../services/extract_metadata.py)モジュールから `extract_metadata_from_document`関数を使用して、ドキュメントテキストからメタデータを抽出し、その結果に従ってメタデータオブジェクトを更新します。 デフォルト値は `False`です。

スクリプトはJSONLファイルを辞書のジェネレータとして開き、データを反復処理し、ドキュメントオブジェクトを作成し、データベースにバッチアップサートします。 スクリプトはいくつかの進行状況メッセージとエラーメッセージ、およびエラー、PII検出、またはメタデータ抽出の問題によりスキップされたアイテムの数と内容を表示します。

`python process_jsonl.py -h` を使用して、オプションとその説明の概要を取得できます。

[example.jsonl](example.jsonl) の例ファイルでスクリプトをテストできます。
