# Weaviate

## Weaviateインスタンスのセットアップ

[Weaviate](https://weaviate.io/)は、数十億のデータオブジェクトにシームレスにスケールするように設計されたオープンソースのベクター検索エンジンです。この実装は、ハイブリッド検索をデフォルトでサポートしています（つまり、キーワード検索のパフォーマンスが向上します）。

Weaviateは4つの方法で実行できます。

- **SaaS** - [Weaviate Cloud Services (WCS)](https://weaviate.io/pricing)を使用して。

  WCSは、ホスティング、スケーリング、およびWeaviateインスタンスの更新を管理する完全にマネージドなサービスです。30日間の無料サンドボックスで試すことができます。

  WCSでSaaS Weaviateインスタンスをセットアップするには：

  1. [Weaviate Cloud Console](https://console.weaviate.io/)に移動します。
  2. WCSアカウントに登録またはサインインします。
  3. 次の設定で新しいクラスターを作成します：
      - `Name` - クラスターの一意の名前。この名前は、このインスタンスにアクセスするために使用されるURLの一部になります。
      - `Subscription Tier` - 無料試用版の場合はSandboxを選択し、他のオプションについては[hello@weaviate.io](mailto:hello@weaviate.io)までお問い合わせください。
      - `Weaviate Version` - デフォルトで最新バージョン。
      - `OIDC Authentication` - デフォルトで有効化されています。これにより、インスタンスにアクセスするためにユーザー名とパスワードが必要になります。
  4. クラスターが準備できるまで数分お待ちください。完了すると緑色のチェックマーク✔️が表示されます。クラスターのURLをコピーしてください。

- **ハイブリッドSaaS**

  > セキュリティやコンプライアンスの理由でデータをオンプレミスに保持する必要がある場合、WeaviateはハイブリッドSaaSオプションも提供しています。Weaviateはあなたのクラウドインスタンス内で実行されますが、クラスターはWeaviateによってリモートで管理されます。これにより、外部パーティにデータを送信せずにマネージドサービスの利点を得ることができます。

  WeaviateハイブリッドSaaSはカスタムソリューションです。このオプションに興味がある場合は、[hello@weaviate.io](mailto:hello@weaviate.io)までお問い合わせください。

- **セルフホスト** - Dockerコンテナを使用する

  DockerでWeaviateインスタンスをセットアップするには：

  1. まだインストールされていない場合は、ローカルマシンに[Dockerをインストール](https://docs.docker.com/engine/install/)します。
  2. [Docker Composeプラグインをインストール](https://docs.docker.com/compose/install/)します。
  3. この`curl`コマンドで`docker-compose.yml`ファイルをダウンロードします：

     ```
     curl -o docker-compose.yml "https://configuration.weaviate.io/v2/docker-compose/docker-compose.yml?modules=standalone&runtime=docker-compose&weaviate_version=v1.18.0"
     ```

     または、Weaviateのdocker compose [設定ツール](https://weaviate.io/developers/weaviate/installation/docker-compose)を使用して独自の`docker-compose.yml`ファイルを生成できます。

  4. Weaviateインスタンスを起動するには`docker compose up -d`を実行します。

     > シャットダウンするには`docker compose down`を実行します。

- **セルフホスト** - Kubernetesクラスターを使用する

  Kubernetesでセルフホストインスタンスを設定するには、Weaviateの[ドキュメント](https://weaviate.io/developers/weaviate/installation/kubernetes)に従ってください。

## Weaviate環境変数の設定

Weaviateインスタンスに接続するために、いくつかの環境変数を設定する必要があります。

**Retrieval App環境変数**

| 名前               | 必須 | 説明                                                                             |
|------------------|----|--------------------------------------------------------------------------------|
| `DATASTORE`      | はい | データストア名。これを`weaviate`に設定してください。                                                |
| `BEARER_TOKEN`   | はい | あなたの[シークレットトークン](/README.md#general-environment-variables)（Weaviateのものではありません） |
| `OPENAI_API_KEY` | はい | あなたのOpenAI APIキー                                                               |

**Weaviate Datastore環境変数**

| 名前               | 必須 | 説明                                    | デフォルト              |
|------------------|----|---------------------------------------|--------------------|
| `WEAVIATE_HOST`  | 任意 | Weaviateインスタンスのホストアドレス（以下の注意事項を参照）    | `http://127.0.0.1` |
| `WEAVIATE_PORT`  | 任意 | Weaviateのポート番号（WCSの場合は443を使用）         | 8080               |
| `WEAVIATE_CLASS` | 任意 | ドキュメントを保存するために選択したWeaviateクラス/コレクション名 | OpenAIDocument     |

> **WCSインスタンスの場合**、`WEAVIATE_PORT`を443に設定し`WEAVIATE_HOST`を`https://(wcs-instance-name).weaviate.network`に設定します。例：`https://my-project.weaviate.network/`。

> **セルフホストインスタンスの場合**、インスタンスが127.0.0.1:8080以外の場所にある場合は`WEAVIATE_HOST`と`WEAVIATE_PORT`をそれに応じて設定します。例：`WEAVIATE_HOST=http://localhost/　`および`WEAVIATE_PORT=4040`。

**Weaviate認証環境変数**

WeaviateインスタンスにOIDC認証を有効にした場合（WCSインスタンスでは推奨）、次の環境変数を設定します。匿名アクセスを有効にした場合は、このセクションをスキップしてください。
**Weaviate認証環境変数**

WeaviateインスタンスにOIDC認証を有効にした場合（WCSインスタンスでは推奨）、次の環境変数を設定します。匿名アクセスを有効にした場合は、このセクションをスキップしてください。

| 名前                  | 必須 | 説明               |
|---------------------|----|------------------|
| `WEAVIATE_USERNAME` | はい | OIDCまたはWCSのユーザー名 |
| `WEAVIATE_PASSWORD` | はい | OIDCまたはWCSのパスワード |
| `WEAVIATE_SCOPES`   | 任意 | スコープのスペース区切りリスト  |
Weaviateの認証については、[Weaviateの認証](https://weaviate.io/developers/weaviate/configuration/authentication#overview)と[Pythonクライアント認証](https://weaviate-python-client.readthedocs.io/en/stable/weaviate.auth.html)を参照してください。

**Weaviateバッチインポート環境変数**

Weaviateはバッチ処理機構を使用して一括で操作を行います。これによりデータのインポートや更新がより速く、効率的になります。以下のオプションの環境変数でバッチ設定を調整できます。

| 名前                               | 必須 | 説明                   | デフォルト |
|----------------------------------|----|----------------------|-------|
| `WEAVIATE_BATCH_SIZE`            | 任意 | バッチ操作ごとの挿入/更新の数      | 20    |
| `WEAVIATE_BATCH_DYNAMIC`         | 任意 | バッチ処理がバッチサイズを決定する    | False |
| `WEAVIATE_BATCH_TIMEOUT_RETRIES` | 任意 | タイムアウト時の再試行回数        | 3     |
| `WEAVIATE_BATCH_NUM_WORKERS`     | 任意 | バッチ操作を実行する並行スレッドの最大数 | 1     |

> **注意:** 最適な`WEAVIATE_BATCH_SIZE`は利用可能なリソース（RAM、CPU）に依存します。値が大きいほど一括操作が速くなりますが、RAMとCPUの要求も高くなります。インポート処理中に失敗が発生した場合はバッチサイズを減らしてください。

> `WEAVIATE_BATCH_SIZE`を`None`に設定すると、バッチサイズに制限がありません。すべての挿入または更新操作が、単一の操作でWeaviateに送信されます。これはリスクがある可能性があります。なぜなら、バッチサイズを制御できなくなるからです。

Weaviateの[バッチ設定](https://weaviate.io/developers/weaviate/client-libraries/python#batch-configuration)について詳しくは、こちらを参照してください。
