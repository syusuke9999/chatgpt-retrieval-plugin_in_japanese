# Fly.ioへのデプロイ

## 未使用の依存関係の削除

アプリをデプロイする前に、[pyproject.toml](./pyproject.toml)ファイルから未使用の依存関係を削除して、アプリのサイズを減らし、パフォーマンスを向上させることができます。使用するベクトルデータベースのプロバイダに応じて、特定のプロバイダに必要でないパッケージを削除できます。

各ベクトルデータベースプロバイダについて削除できるパッケージを[ここ](./removing-unused-dependencies.md)で見つけることができます。

`pyproject.toml`ファイルから不要なパッケージを削除した後は、`poetry lock`と`poetry install`を手動で実行する必要はありません。提供されたDockerfileは、`poetry export`コマンドによって生成された`requirements.txt`ファイルを使用して必要な依存関係をインストールすることを取り扱います。

## デプロイ

このリポジトリからDockerコンテナをFly.ioにデプロイする手順は以下のとおりです。

すでにローカルマシンに[Dockerをインストール](https://docs.docker.com/engine/install/)していない場合はインストールしてください。

ローカルマシンに[Fly.io CLI](https://fly.io/docs/getting-started/installing-flyctl/)をインストールしてください。

GitHubからリポジトリをクローンします。

```
git clone https://github.com/openai/chatgpt-retrieval-plugin.git
```

クローンされたリポジトリディレクトリに移動してください。

```
cd path/to/chatgpt-retrieval-plugin
```

Fly.io CLIにログインしてください。

```
flyctl auth login
```

Fly.ioアプリを作成して起動します。

```
flyctl launch
```

ターミナルの指示に従ってください。

- アプリ名を選択します
- アプリのリージョンを選択します
- データベースを追加しないでください
- まだデプロイしないでください（環境変数がまだ設定されていないため、最初のデプロイが失敗する可能性があります）

必要な環境変数を設定してください。

```
flyctl secrets set DATASTORE=your_datastore \
OPENAI_API_KEY=your_openai_api_key \
BEARER_TOKEN=your_bearer_token \
<あなたが選んだベクターDBの環境変数を追加してください>
```

または、環境変数を[Fly.ioコンソール](https://fly.io/dashboard)に設定することもできます。

この時点で、プラグインマニフェストファイル[ここ](./.well-known/ai-plugin.json)とOpenAPIスキーマ[ここ](./.well-known/openapi.yaml)のプラグインURLをFly.ioアプリのURLに変更することができます。これは`https://your-app-name.fly.dev`になります。

アプリをデプロイするには：

```
flyctl deploy
```

これらの手順を完了すると、DockerコンテナがFly.ioにデプロイされ、必要な環境変数が設定されて実行されます。以下のコマンドを実行してアプリを表示できます。

```
flyctl open
```

これにより、アプリのURLが開かれます。OpenAPIスキーマは`<your_app_url>./.well-known/openapi.yaml`、マニフェストは`<your_app_url>./.well-known/ai-plugin.json`にあります。

アプリのログを表示するには：

```
flyctl logs
```

次に、プラグインマニフェストファイル[ここ](./.well-known/ai-plugin.json)とOpenAPIスキーマ[ここ](./.well-known/openapi.yaml)のプラグインURLを変更し、`flyctl deploy`で再デプロイしてください。このURLは`https://<your-app-name>.fly.dev`になります。

**デバッグのヒント：**
Fly.ioはデフォルトでポート8080を使用しています。

アプリがデプロイに失敗した場合は、環境変数が正しく設定されているかを確認し、ポートが正しく構成されていることを確認してください。また、[`-e`フラグ](https://fly.io/docs/flyctl/launch/)を`flyctl launch`コマンドで使用して、起動時に環境変数を設定することもできます。
