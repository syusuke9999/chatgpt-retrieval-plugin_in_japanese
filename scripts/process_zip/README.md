## ZIPファイルの処理

このスクリプトは、ドキュメントのファイルダンプを処理し、メタデータと一緒にベクトルデータベースに保存するためのユーティリティです。オプションで、言語モデルを使用して個人情報を検出してスキップすることもできます。また、スクリプトは、言語モデルを使用してドキュメントからメタデータを抽出することもできます。[`services/pii_detection`](../../services/pii_detection.py)のPII検出関数と[`services/extract_metadata`](../../services/extract_metadata.py)のメタデータ抽出関数をカスタマイズすることができます。

## 使用方法

ターミナルからこのスクリプトを実行するには、このフォルダに移動し、以下のコマンドを使用します。

```
python process_zip.py --filepath path/to/file_dump.zip --custom_metadata '{"source": "email"}' --screen_for_pii True --extract_metadata True
```

ここで：

- `path/to/file_dump.zip`は、処理するファイルダンプの名前またはパスです。このzipファイルの形式は、docx、pdf、txt、md、pptxファイルを含むzipファイルである必要があります（内部フォルダ構造は任意のものが使用できます）。
- `--custom_metadata`は、ドキュメントのメタデータを更新するためのオプションのJSON文字列です。たとえば、`{"source": "file"}`は、メタデータの各ドキュメントに`source`フィールドを値`file`で追加します。デフォルト値は空のJSONオブジェクト（ `{}`）です。
- `--screen_for_pii`は、PII検出関数を使用するかどうかを示すオプションのブールフラグです。`True`に設定された場合、スクリプトは[`services/pii_detection`](../../services/pii_detection.py)モジュールの`screen_text_for_pii`関数を使用して、言語モデルを使用してドキュメントテキストにPIIが含まれているかどうかを確認します。 PIIが検出されると、スクリプトは警告を表示してドキュメントをスキップします。デフォルト値は`False`です。
- `--extract_metadata`は、言語モデルを使用してドキュメントからメタデータを抽出し、メタデータオブジェクトを更新するかどうかを示すオプションのブールフラグです。`True`に設定された場合、スクリプトは[`services/extract_metadata`](../../services/extract_metadata.py)モジュールの`extract_metadata_from_document`関数を使用して、ドキュメントテキストからメタデータを抽出し、メタデータオブジェクトを更新します。デフォルト値は `False`です。

スクリプトは、ファイルをzipファイルから一時ディレクトリに抽出し、各ファイルを処理してドキュメントテキストとメタデータをデータベースに保存し、その後一時ディレクトリとその内容を削除します。必要に応じて、一部の進行状況メッセージとエラーメッセージを表示します。

オプションと説明の概要を取得するには、`python process_zip.py -h`を使用することができます。

[example.zip](example.zip)のサンプルファイルでスクリプトをテストできます。
