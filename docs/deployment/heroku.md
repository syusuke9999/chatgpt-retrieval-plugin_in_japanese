# Herokuへのデプロイ

## 未使用の依存関係の削除

アプリをデプロイする前に、[pyproject.toml](/pyproject.toml)ファイルから未使用の依存関係を削除して、アプリのサイズを減らし、パフォーマンスを向上させることができます。使用するベクトルデータベースプロバイダに応じて、特定のプロバイダに必要ないパッケージを削除することができます。

各ベクトルデータベースプロバイダに対して削除できるパッケージを[ここ](removing-unused-dependencies.md)で見つけることができます。

`pyproject.toml`ファイルから不要なパッケージを削除した後、`poetry lock`と`poetry install`を手動で実行する必要はありません。提供されたDockerfileは、`poetry export`コマンドによって生成された`requirements.txt`ファイルを使用して必要な依存関係をインストールすることを担当します。

## デプロイ

このリポジトリからDockerコンテナをHerokuにデプロイし、必要な環境変数を設定するには、次の手順に従ってください。

ローカルマシンに[Dockerをインストール](https://docs.docker.com/engine/install/)していない場合は、インストールしてください。

ローカルマシンに[Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli)をインストールしてください。

GitHubからリポジトリをクローンします。

```
git clone https://github.com/openai/chatgpt-retrieval-plugin.git
```

クローンしたリポジトリディレクトリに移動します。

```
cd path/to/chatgpt-retrieval-plugin
```

Heroku CLIにログインします。

```
heroku login
```

Herokuアプリを作成します。

```
heroku create [app-name]
```

Heroku Container Registryにログインします。

```
heroku container:login
```

または、Makefileからコマンドを使用してHeroku Container Registryにログインすることができます。

```
make heroku-login
```

Dockerfileを使用してDockerイメージをビルドします。

```
docker buildx build --platform linux/amd64 -t [image-name] .
```

(`[image-name]`をDockerイメージに付けたい名前に置き換えてください)

DockerイメージをHeroku Container Registryにプッシュし、新しくプッシュされたイメージをHerokuアプリにリリースします。

```
docker tag [image-name] registry.heroku.com/[app-name]/web
docker push registry.heroku.com/[app-name]/web
heroku container:release web -a [app-name]
```

または、コマンドを使用してDockerイメージをHeroku Container Registryにプッシュすることができます。

```
make heroku-push
```

**注意:** Makefileを編集して、`<your app name>`を実際のアプリ名に置き換える必要があります。

Herokuアプリの必要な環境変数を設定します。

```
heroku config:set DATASTORE=your_datastore \
OPENAI_API_KEY=your_openai_api_key \
BEARER_TOKEN=your_bearer_token \
<選択したベクトルDBの環境変数を追加してください> \
-a [app-name]
```
