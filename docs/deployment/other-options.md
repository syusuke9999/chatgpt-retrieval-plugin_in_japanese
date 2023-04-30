# 他のデプロイオプション

アプリをデプロイするための他の選択肢は以下の通りです。

- **Azure Container Apps**: これは、Dockerコンテナを使用してWebアプリをデプロイおよび管理するためのクラウドプラットフォームです。Azure CLIまたはAzure Portalを使用してアプリサービスを作成および構成し、Dockerイメージをコンテナレジストリにプッシュしてからアプリサービスにデプロイできます。Azure Portalを使用して環境変数を設定し、アプリをスケールアップすることもできます。詳細は[こちら](https://learn.microsoft.com/en-us/azure/container-apps/get-started-existing-container-image-portal?pivots=container-apps-private-registry)。
- **Google Cloud Run**: これは、Dockerコンテナを使用してステートレスなWebアプリを実行するためのサーバーレスプラットフォームです。Google Cloud Consoleまたはgcloudコマンドラインツールを使用して、Cloud Runサービスを作成およびデプロイし、DockerイメージをGoogle Container Registryにプッシュしてからサービスにデプロイできます。Google Cloud Consoleを使用して環境変数を設定し、アプリをスケールアップすることもできます。詳細は[こちら](https://cloud.google.com/run/docs/quickstarts/build-and-deploy)。
- **AWS Elastic Container Service**: これは、Dockerコンテナを使用してWebアプリを実行および管理するためのクラウドプラットフォームです。AWS CLIまたはAWS Management Consoleを使用して、ECSクラスタを作成および構成し、DockerイメージをAmazon Elastic Container Registryにプッシュしてからクラスタにデプロイできます。AWS Management Consoleを使用して環境変数を設定し、アプリをスケールアップすることもできます。詳細は[こちら](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/docker-basics.html)。

アプリを作成した後、プラグインのURLを[こちら](/.well-known/ai-plugin.json)のプラグインマニフェストファイルと、[こちら](/.well-known/openapi.yaml)のOpenAPIスキーマで変更して、再デプロイしてください。

## 未使用の依存関係の削除

アプリをデプロイする前に、[pyproject.toml](/pyproject.toml)ファイルから未使用の依存関係を削除して、アプリのサイズを縮小し、パフォーマンスを向上させることができます。選択したベクターデータベースプロバイダーに応じて、特定のプロバイダーに必要のないパッケージを削除できます。

それぞれのベクターデータベースプロバイダーに対して削除できるパッケージは[こちら](removing_unused_dependencies.md)で見つけられます。

`pyproject.toml`ファイルから不要なパッケージを削除した後、`poetry lock`および`poetry install`を手動で実行する必要はありません。提供されたDockerfileは、`poetry export`コマンドによって生成された`requirements.txt`ファイルを使用して、必要な依存関係をインストールします。
