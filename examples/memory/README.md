# ChatGPT Retrieval Plugin with Memory（メモリ付きChatGPT検索プラグイン）

この例では、ChatGPTに会話から情報を覚えさせ、後で使用するために検索プラグインに保存する方法を示しています。モデルが`/upsert`エンドポイントにアクセスできるようにすることで、会話のスニペットをベクターデータベースに保存し、必要に応じて取得できます。

## セットアップ

ChatGPTに会話から情報を保存する機能を有効にするには、以下の手順に従ってください。

- [openapi.yaml](openapi.yaml)の内容を、メインの[openapi.yaml](../../.well-known/openapi.yaml)ファイルにコピーします。

- [ai-plugin.json](ai-plugin.json)の内容を、メインの[ai-plugin.json](../../.well-known/ai-plugin.json)ファイルにコピーします。

**オプション:** プラグインの指示やメタデータモデルに変更を加えた場合、[main.py](main.py)の内容をメインの[main.py](../../server/main.py)ファイルにコピーすることもできます。これにより、アプリをローカルで実行した際に`http://0.0.0.0:8000/sub/openapi.json`でopenapi.jsonにアクセスできます。JSONをYAML形式に変換するには、[Swagger Editor](https://editor.swagger.io/)を使用できます。また、openapi.yamlファイルをopenapi.jsonファイルに置き換えることもできます。

これらの手順を完了すると、ChatGPTはプラグインの`/upsert`エンドポイントにアクセスし、会話のスニペットをベクターデータベースに保存できるようになります。これにより、モデルは以前の会話から情報を覚えておくことができ、必要に応じてそれを取得できます。
