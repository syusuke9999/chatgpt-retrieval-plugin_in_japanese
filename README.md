# ChatGPT Retrieval Plugin

> **[ChatGPTプラグインの待ち行列に参加する](https://openai.com/waitlist/plugins)!**

2018年から2022年までの国連年次報告にアクセスできるRetrieval Pluginの例を[こちら](https://cdn.openai.com/chat-plugins/retrieval-gh-repo-readme/Retrieval-Final.mp4)で見つけることができます。

## はじめに

ChatGPT Retrieval Pluginリポジトリでは、自然言語のクエリを使用して個人や組織のドキュメントを意味的に検索および取得する柔軟なソリューションが提供されています。リポジトリはいくつかのディレクトリに編成されています。

| ディレクトリ                        | 説明                                                                |
|-------------------------------|-------------------------------------------------------------------|
| [`datastore`](/datastore)     | さまざまなベクトルデータベースプロバイダを使用してドキュメントの埋め込みを保存およびクエリするためのコアロジックが含まれています。 |
| [`docs`](/docs)               | 各ベクトルデータベースプロバイダの設定と使用方法、Webフック、未使用の依存関係の削除に関するドキュメントが含まれています。    |
| [`examples`](/examples)       | 例の設定、認証方法、プロバイダ固有の例が提供されています。                                     |
| [`models`](/models)           | プラグインで使用されるデータモデルが含まれています（ドキュメントやメタデータのモデルなど）。                    |
| [`scripts`](/scripts)         | 異なるデータソースからのドキュメントの処理とアップロード用のスクリプトが提供されています。                     |
| [`server`](/server)           | FastAPIサーバーのメイン実装が含まれています。                                        |
| [`services`](/services)       | チャンク分割、メタデータ抽出、PII検出などのタスク用のユーティリティサービスが含まれています。                  |
| [`tests`](/tests)             | 各ベクトルデータベースプロバイダの統合テストが含まれています。                                   |
| [`.well-known`](/.well-known) | プラグインのマニフェストファイルとOpenAPIスキーマが格納されており、プラグインの設定とAPI仕様が定義されています。     |

このREADMEでは、ChatGPT Retrieval Pluginのセットアップ、開発、デプロイに関する詳細情報が提供されています。

## 目次

- [クイックスタート](#クイックスタート)
- [概要](#概要)
  - [プラグイン](#プラグイン)
  - [Retrieval Plugin](#retrieval-plugin)
  - [メモリ機能](#メモリ機能)
  - [セキュリティ](#セキュリティ)
  - [APIエンドポイント](#APIエンドポイント)
- [開発](#開発)
  - [セットアップ](#セットアップ)
    - [一般的な環境変数](#一般的な環境変数)
  - [ベクトルデータベースの選択](#ベクトルデータベースの選択)
    - [Pinecone](#pinecone)
    - [Weaviate](#weaviate)
    - [Zilliz](#zilliz)
    - [Milvus](#milvus)
    - [Qdrant](#qdrant)
    - [Redis](#redis)
  - [APIをローカルで実行する](#APIをローカルで実行する)
  - [ChatGPTでのlocalhostプラグインのテスト](#ChatGPTでのlocalhostプラグインのテスト)
  - [パーソナライゼーション](#パーソナライズ)
  - [認証方法](#認証方法)
- [デプロイメント](#デプロイ)
- [開発者プラグインのインストール](#開発者プラグインのインストール)
- [Webフック](#WebHook)
- [スクリプト](#スクリプト)
- [制限事項](#制限事項)
- [貢献者](#貢献者)
- [今後の展開](#今後の方向性)

## クイックスタート

ChatGPT Retrieval Pluginを素早くセットアップして実行するには、以下の手順を実行します。

1. まだインストールしていない場合は、Python 3.10をインストールします。
2. リポジトリをクローンします: `git clone https://github.com/openai/chatgpt-retrieval-plugin.git`
3. クローンされたリポジトリのディレクトリに移動します: `cd /path/to/chatgpt-retrieval-plugin`
4. poetryをインストールします: `pip install poetry`
5. Python 3.10を使用した新しい仮想環境を作成します: `poetry env use python3.10`
6. 仮想環境をアクティブ化します: `poetry shell`
7. アプリの依存関係をインストールします: `poetry install`
8. [bearerトークン](#一般的な環境変数)を作成します
9. 必要な環境変数を設定します:

```
   export DATASTORE=<your_datastore>
   export BEARER_TOKEN=<your_bearer_token>
   export OPENAI_API_KEY=<your_openai_api_key>

   # 選択したベクトルDBの環境変数を追加します.
   # これらの一部はオプションです。詳細については/docs/providersのプロバイダのセットアップドキュメントを参照してください.

   # Pinecone
   export PINECONE_API_KEY=<your_pinecone_api_key>
   export PINECONE_ENVIRONMENT=<your_pinecone_environment>
   export PINECONE_INDEX=<your_pinecone_index>

   # Weaviate
   export WEAVIATE_HOST=<your_weaviate_instance_url>
   export WEAVIATE_PORT=<your_weaviate_port_443_for_WCS>
   export WEAVIATE_CLASS=<your_optional_weaviate_class>
   export WEAVIATE_USERNAME=<your_weaviate_WCS_username>
   export WEAVIATE_PASSWORD=<your_weaviate_WCS_password>
   export WEAVIATE_SCOPES=<your_optional_weaviate_scopes>
   export WEAVIATE_BATCH_SIZE=<optional_weaviate_batch_size>
   export WEAVIATE_BATCH_DYNAMIC=<optional_weaviate_batch_dynamic>
   export WEAVIATE_BATCH_TIMEOUT_RETRIES=<optional_weaviate_batch_timeout_retries>
   export WEAVIATE_BATCH_NUM_WORKERS=<optional_weaviate_batch_num_workers>

   # Zilliz
   export ZILLIZ_COLLECTION=<your_zilliz_collection>
   export ZILLIZ_URI=<your_zilliz_uri>
   export ZILLIZ_USER=<your_zilliz_username>
   export ZILLIZ_PASSWORD=<your_zilliz_password>

   # Milvus
   export MILVUS_COLLECTION=<your_milvus_collection>
   export MILVUS_HOST=<your_milvus_host>
   export MILVUS_PORT=<your_milvus_port>
   export MILVUS_USER=<your_milvus_username>
   export MILVUS_PASSWORD=<your_milvus_password>

   # Qdrant
   export QDRANT_URL=<あなたのQdrant URL>
   export QDRANT_PORT=<あなたのQdrantポート番号>
   export QDRANT_GRPC_PORT=<あなたのQdrant GRPCポート番号>
   export QDRANT_API_KEY=<あなたのQdrant APIキー>
   export QDRANT_COLLECTION=<あなたのQdrantコレクション名>

   # Redis
   export REDIS_HOST=<あなたのRedisホスト>
   export REDIS_PORT=<あなたのRedisポート番号>
   export REDIS_PASSWORD=<あなたのRedisパスワード>
   export REDIS_INDEX_NAME=<あなたのRedisインデックス名>
   export REDIS_DOC_PREFIX=<あなたのRedisドキュメントプレフィックス>
   export REDIS_DISTANCE_METRIC=<あなたのRedis距離計量>
   export REDIS_INDEX_TYPE=<あなたのRedisインデックスタイプ>
```

10. APIをローカルで実行します: `poetry run start`
11. `http://0.0.0.0:8000/docs` でAPIドキュメントにアクセスし、APIエンドポイントのテスト（bearerトークンを追加することを確認してください）。

### ChatGPTでのテスト

ローカルにホストされたプラグインをChatGPTでテストするには、以下の手順を実行します。

1. APIをlocalhostで実行します: `poetry run dev`
2. READMEの[ChatGPTでのlocalhostプラグインのテスト](#ChatGPTでのlocalhostプラグインのテスト)セクションの手順に従ってください。

ChatGPT Retrieval Pluginのセットアップ、開発、およびデプロイに関する詳細情報については、以下の開発セクションを参照してください。

## 概要

### プラグイン

プラグインは、ChatGPTのような言語モデル向けに設計されたチャット拡張機能であり、ユーザーのリクエストに応じて最新の情報にアクセスしたり、計算を実行したり、サードパーティ製サービスと連携したりします。プラグインにより、様々なユースケースを実現し、言語モデルの機能を向上させることができます。

開発者は、APIを自分のウェブサイトで公開し、APIを説明する標準化されたマニフェストファイルを提供することで、プラグインを作成できます。ChatGPTはこれらのファイルを利用し、AIモデルが開発者が定義したAPIにコールを行うことができます。

プラグインは以下の構成要素で構成されています。

- API
- APIスキーマ（OpenAPI JSONまたはYAML形式）
- プラグインに関連するメタデータを定義するマニフェスト（JSONファイル）

Retrieval Pluginにはすでにこれらの構成要素が含まれています。Chatプラグインに関するブログ記事は[こちら](https://openai.com/blog/chatgpt-plugins)から、ドキュメントは[こちら](https://platform.openai.com/docs/plugins/introduction)からご覧いただけます。

### Retrieval Plugin

これは、個人や組織のドキュメントを意味的に検索および取得するためのChatGPT用プラグインです。ファイル、ノート、電子メールなどのデータソースから、質問や要求を自然言語で表現することによって、最も関連性の高いドキュメントのスニペットを取得できます。企業は、このプラグインを使用して、従業員向けに社内ドキュメントをChatGPTで利用できるようにすることができます。

プラグインでは、OpenAIの`text-embedding-ada-002`埋め込みモデルを使用してドキュメントのチャンクの埋め込みを生成し、それらをバックエンドのベクトルデータベースに保存して検索します。オープンソースで自己ホスト型の解決策として、開発者は独自のRetrieval Pluginをデプロイし、ChatGPTに登録することができます。また、Retrieval Pluginは複数のベクトルデータベースプロバイダをサポートしており、開発者はリストから好みのものを選択できます。

FastAPIサーバーは、ドキュメントをupsert（アップサート）、クエリ、削除するためのプラグインのエンドポイントを公開します。ユーザーは、ソース、日付、著者などのメタデータフィルタを使用して検索結果を絞り込むことができます。プラグインは、Dockerコンテナをサポートする任意のクラウドプラットフォーム（Fly.io、Heroku、Azure Container Appsなど）でホストできます。upsertエンドポイントとdeleteエンドポイントにインバウンドWebフックを使用して、プラグインはさまざまなデータソースからのドキュメントを継続的に処理して保存し、ベクトルデータベースを最新の状態に保つことができます。[Zapier](https://zapier.com)や[Make](https://www.make.com)などのツールを使って、イベントやスケジュールに基づいてWebフックを設定できます。

### メモリ機能

Retrieval Pluginの目立つ機能のひとつは、ChatGPTにメモリを提供する能力です。ユーザーが促す場合に限り、プラグインのupsertエンドポイントを利用して、ChatGPTは会話のスニペットをベクトルデータベースに保存し、後で参照することができます。この機能により、ChatGPTは以前の会話から情報を思い出したり、取得したりすることができ、よりコンテキストに対する認識があるチャット体験が実現します。Retrieval Pluginでメモリを構成する方法については、[こちら](/examples/memory)を参照してください。

### セキュリティ

Retrieval Pluginは、ChatGPTがベクトルデータベースのコンテンツを検索し、その結果をChatGPTセッションに追加することができます。これにより、外部の影響はなく、主なリスクの考慮点はデータの承認とプライバシーです。開発者は、自分たちが承認を得ており、ユーザーのChatGPTセッションに表示されても問題ないコンテンツを、Retrieval Pluginに追加すべきです。プラグインを保護するために、さまざまな認証方法から選択できます（詳細については[こちら](#認証方法)を参照してください）。

### APIエンドポイント

Retrieval Pluginは、PythonでAPIを構築するためのWebフレームワークであるFastAPIを使用して構築されています。FastAPIは、APIエンドポイントの開発、検証、ドキュメント作成を容易にします。FastAPIのドキュメントは[こちら](https://fastapi.tiangolo.com/)で確認できます。

FastAPIの利点の1つは、Swagger UIを使用したインタラクティブなAPIドキュメントの自動生成です。APIがローカルで実行されている場合、`<local_host_url（例：http://0.0.0.0:8000）>/docs`のSwagger UIを使用してAPIエンドポイントと対話したり、機能をテストしたり、リクエストとレスポンスモデルを確認できます。

プラグインは、ベクトルデータベースからドキュメントを追加・照会・削除する以下のエンドポイントを公開しています。すべてのリクエストとレスポンスはJSON形式で、承認ヘッダとして有効なベアラートークンが必要です。

- `/upsert`：このエンドポイントを使用して、1つ以上のドキュメントをアップロードし、テキストとメタデータをベクトルデータベースに保存できます。ドキュメントは約200トークンのチャンクに分割され、一意のIDが付与されます。 エンドポイントは、リクエスト本文内のドキュメントのリスト（`text`フィールドを伴う）を受け付けるほか、`id`および`metadata`フィールドをオプションで受け付けます。`metadata`フィールドには、`source`、`source_id`、`url`、`created_at`、そして`author` を含むオプションのサブフィールドがあります。エンドポイントは、挿入されたドキュメントのIDのリストを返します（IDが最初に提供されなかった場合には生成されます）。

- `/upsert-file`： このエンドポイントを使用して、1つのファイル（PDF、TXT、DOCX、PPTX、またはMD）をアップロードし、そのテキストとメタデータをベクトルデータベースに保存できます。ファイルはプレーンテキストに変換され、約200トークンのチャンクに分割され、それぞれに一意のIDが付与されます。エンドポイントは挿入されたファイルの生成されたIDを含むリストを返します。

- `/query`：このエンドポイントを使用して、1つ以上の自然言語クエリとオプションのメタデータフィルタを使用して、ベクトルデータベースを照会できます。 エンドポイントは、リクエスト本文内のクエリのリスト（`query`フィールドを伴う）を受け付けるほか、`filter`および`top_k`フィールドをオプションで受け付けます。`filter`フィールドは、`source`、`source_id`、`document_id`、`url`、`created_at`、そして`author` を含むサブフィールドのサブセットを含む必要があります。`top_k`フィールドは、各クエリに対して何件の結果を返すかを指定し、デフォルト値は3です。 エンドポイントは、各オブジェクトのリストを返し、それぞれが与えられたクエリに対する最も関連性の高いドキュメントのチャンク、テキスト、メタデータ、類似性スコアのリストを含みます。

- `/delete`：このエンドポイントを使用して、ID、メタデータフィルタ、または delete_all フラグを使用して、1つ以上のドキュメントをベクトルデータベースから削除できます。 エンドポイントは、リクエスト本文内で以下のパラメータの最低1つを受け付けます。`ids`、`filter`、または`delete_all`です。`ids`パラメータは、削除するドキュメントIDのリストで、これらのIDに関連するすべてのドキュメントのチャンクが削除されます。`filter`パラメータは、`source`、`source_id`、`document_id`、`url`、`created_at`、そして`author` を含むサブフィールドのサブセットを含む必要があります。`delete_all`パラメータは、ベクトルデータベースからすべてのドキュメントを削除するかどうかを示す真偽値です。エンドポイントは、削除が成功したかどうかを示す真偽値を返します。

詳細な仕様とリクエスト/レスポンスモデルの例は、アプリをローカルで実行してから http://0.0.0.0:8000/openapi.json にアクセスするか、OpenAPIスキーマ[こちら](/.well-known/openapi.yaml)に記載されています。ただし、OpenAPIスキーマには`/query`エンドポイントのみが含まれています。これは、ChatGPTがアクセスする必要がある唯一の機能だからです。この方法では、ChatGPTは自然言語のクエリやニーズに基づいて関連するドキュメントを取得するだけでなく、プラグインを使用して、会話からのスニペットをベクトルデータベースに保存することで、後で情報を覚えておく機能も追加できます。`/upsert`エンドポイントへのアクセスを許可するマニフェストとOpenAPIスキーマの例は[こちら](/examples/memory)で確認できます。

カスタムメタデータフィールドを追加するには、`DocumentMetadata`および`DocumentMetadataFilter`データモデル[model/models.py](/models/models.py)を編集し、OpenAPIスキーマ[here](/.well-known/openapi.yaml)を更新します。アプリをローカルで実行し、http://0.0.0.0:8000/sub/openapi.jsonで見つかったJSONをコピーして、[Swagger Editor](https://editor.swagger.io/)でYAML形式に変換するだけです。 あるいは、`openapi.yaml`ファイルを`openapi.json`ファイルに置き換えることもできます。

## 開発

### セットアップ

このアプリはPython 3.10と[poetry](https://python-poetry.org/)を使い、依存関係の管理を行っています。

お使いのマシンにPython 3.10がインストールされていない場合は、公式の[Pythonのウェブサイト](https://www.python.org/downloads/)からインストールするか、または`brew`や`apt`などのパッケージ管理システムでインストールしてください。

GitHubからリポジトリをクローンします:

```
git clone https://github.com/openai/chatgpt-retrieval-plugin.git
```

クローンしたリポジトリのディレクトリに移動します:

```
cd /path/to/chatgpt-retrieval-plugin
```

poetryをインストール:

```
pip install poetry
```

Python 3.10を使用した新しい仮想環境を作成します:

```
poetry env use python3.10
poetry shell
```

poetryを使ってアプリの依存関係をインストールします:

```
poetry install
```

**注意:** `pyproject.toml`に依存関係を追加する場合は、`poetry lock`と`poetry install`を実行してください。

#### 一般的な環境変数

APIは、以下の環境変数が必要です:

| 名前               | 必須 | 説明                                                                                                                         |
|------------------|----|----------------------------------------------------------------------------------------------------------------------------|
| `DATASTORE`      | はい | これは、埋め込みデータの保存とクエリに使用するベクターデータベースプロバイダを指定するものです。`pinecone`、`weaviate`、`zilliz`、`milvus`、`qdrant`、`redis`から選ぶことができます。       |
| `BEARER_TOKEN`   | はい | これは、APIへのリクエストを認証するために必要なシークレットトークンです。[jwt.io](https://jwt.io/)など、お好みのツールや方法で生成できます。                                       |
| `OPENAI_API_KEY` | はい | これは、`text-embedding-ada-002`モデルを使用して埋め込みを生成するために必要なOpenAI APIキーです。[OpenAI](https://openai.com/)のアカウントを作成することでAPIキーを取得できます。 |

### ベクトルデータベースの選択

プラグインは、いくつかのベクターデータベースプロバイダをサポートしており、それぞれ異なる機能、パフォーマンス、価格があります。選択するものによって、異なるDockerfileを使用したり、異なる環境変数を設定したりする必要があります。以下のセクションでは、各ベクターデータベースプロバイダについて簡単に説明しています。

各ベクターデータベースプロバイダの設定方法や使用方法については、`/docs/providers/<datastore_name>/setup.md`ファイル（[こちらのフォルダ](/docs/providers)）にあるドキュメントを参照してください。

#### Pinecone

[Pinecone](https://www.pinecone.io)は、高速性、スケーラビリティ、迅速なプロダクションへの展開を目的としたマネージド型のベクターデータベースです。ハイブリッド検索をサポートし、現在SPLADE疎ベクトルのネイティブサポートを提供している唯一のデータストアです。詳しい設定方法は、[`/docs/providers/pinecone/setup.md`](/docs/providers/pinecone/setup.md)を参照してください。

#### Weaviate

[Weaviate](https://weaviate.io/)は、何十億ものデータオブジェクトにシームレスにスケーリングできるオープンソースのベクター検索エンジンです。ハイブリッド検索が最初からサポートされており、効率的なキーワード検索が必要なユーザーに適しています。Weaviateは、自己ホスト型またはマネージド型で提供されており、デプロイメントの柔軟性があります。詳しい設定方法は、[`/docs/providers/weaviate/setup.md`](/docs/providers/weaviate/setup.md)を参照してください。

#### Zilliz

[Zilliz](https://zilliz.com)は、何十億規模のデータ向けに設計されたマネージド型のクラウドネイティブベクターデータベースです。多様なインデックスアルゴリズム、距離指標、スカラーのフィルタリング、タイムトラベル検索、スナップショットによるロールバック、完全なRBAC、99.9%の稼働率、別個のストレージとコンピューティング、マルチ言語SDKを含む幅広い機能を提供しています。詳しい設定方法は、[`/docs/providers/zilliz/setup.md`](/docs/providers/zilliz/setup.md)を参照してください。

#### Milvus

[Milvus](https://milvus.io/)は、何十億ものベクターにスケーリング可能なオープンソースのクラウドネイティブベクターデータベースです。Zillizのオープンソース版であり、さまざまなインデックスアルゴリズム、距離指標、スカラーのフィルタリング、タイムトラベル検索、スナップショットによるロールバック、マルチ言語SDK、ストレージとコンピューティングの分離、クラウドのスケーラビリティなど、多くの機能を共有しています。詳しい設定方法は、[`/docs/providers/milvus/setup.md`](/docs/providers/milvus/setup.md)を参照してください。

#### Qdrant

[Qdrant](https://qdrant.tech/)は、ドキュメントとベクター埋め込みを保存できるベクターデータベースです。オープンソースの自己ホスト型デプロイメントとマネージド型の[Qdrant Cloud](https://cloud.qdrant.io/)デプロイメントの両方を提供しており、さまざまな要件を持つユーザーに柔軟性を提供します。詳しい設定方法は、[`/docs/providers/qdrant/setup.md`](/docs/providers/qdrant/setup.md)を参照してください。

#### Redis

[Redis](https://redis.com/solutions/use-cases/vector-database/)は、日常的なアプリケーションやAI/MLワークロードを含むさまざまなユースケースに適したリアルタイムデータプラットフォームです。[Redis Stack docker コンテナ](/examples/docker/redis/docker-compose.yml)を使用して Redis データベースを作成することで、低遅延のベクターエンジンとして使用できます。ホスト/管理されたソリューションとして、[Redis Cloud](https://app.redislabs.com/#/)が利用可能です。詳しいセットアップ手順については、[`/docs/providers/redis/setup.md`](/docs/providers/redis/setup.md)を参照してください。

#### LlamaIndex

[LlamaIndex](https://github.com/jerryjliu/llama_index) は、LLM（Likelihood Learning Machines、尤度学習マシン）を外部データと接続するための中央インターフェイスです。
ChatGPT との互換性のある非構造化データや構造化データを使用して、インメモリインデックスのスイートを提供します。
標準的なベクトルデータベースとは異なり、LlamaIndex はさまざまなインデックス戦略（例：ツリー、キーワードテーブル、ナレッジグラフ）をサポートしており、使用ケースに最適化されています。
軽量で使いやすく、追加のデプロイメントは必要ありません。
設定する必要があるのは、いくつかの環境変数を指定すること（オプションで既存の保存されたインデックス JSON ファイルを指定）だけです。
ただし、クエリのメタデータフィルタはまだサポートされていません。
詳細な設定手順については、 [`/docs/providers/llama/setup.md`](/docs/providers/llama/setup.md) を参照してください。

### APIをローカルで実行する

API をローカルで実行するには、まず、`export` コマンドを使用して必要な環境変数を設定する必要があります。

```
export DATASTORE=<あなたのデータストア>
export BEARER_TOKEN=<あなたのベアラートークン>
export OPENAI_API_KEY=<あなたの_openai_api_キー>
<ここに選択したベクトルDBの環境変数を追加してください>
```

API を以下で開始します。

```
poetry run start
```

ターミナルに表示される URL に `docs` を追加し、ブラウザで開いて API のドキュメントにアクセスし、エンドポイントを試す（例：http://0.0.0.0:8000/docs）。Bearer トークンを入力し、API エンドポイントをテストしてください。

**注：** pyproject.toml ファイルに新しい依存関係を追加する場合は、`poetry lock` および `poetry install` を実行して、ロックファイルを更新し、新しい依存関係をインストールする必要があります。

### ChatGPTでのlocalhostプラグインのテスト

ローカルホストプラグインを ChatGPT でテストするには、提供された [`local-server/main.py`](/local-server/main.py) ファイルを使用してください。これは、CORS 設定、認証なし、マニフェスト、OpenAPI スキーマ、ロゴ用のルートが含まれているローカルホストテスト用に特別に設定されています。

ローカルホストプラグインをテストするには、以下の手順を実行してください。

1. `poetry run dev` コマンドを使用してローカルホストサーバを実行します。これにより、デフォルトのアドレス（例：`localhost:3333`）でサーバが起動されます。

2. [ChatGPT](https://chat.openai.com/) を開き、モデルピッカーから「プラグイン」を選択します。プラグインピッカーをクリックし、リストの一番下にある「プラグインストア」をクリックしてください。

3. 「あなた独自のプラグインを開発する」を選択し、求められたとおりにローカルホスト URL（例：`localhost:3333`）を入力します。

4. これで、あなたのローカルホストプラグインが ChatGPT セッションで有効になりました。

詳細については、[OpenAI documentation](https://platform.openai.com/docs/plugins/getting-started/openapi-definition) を参照してください。

### パーソナライズ

次の方法で、検索プラグインをあなた自身の使用ケースに合わせてカスタマイズすることができます。

- **ロゴを置き換える**：[logo.png](/.well-known/logo.png) の画像をあなた自身のロゴに置き換えてください。

- **データモデルを編集する**：[models.py](/models/models.py) の `DocumentMetadata` と `DocumentMetadataFilter` データモデルを編集して、カスタムメタデータフィールドを追加します。[openapi.yaml](/.well-known/openapi.yaml) の OpenAPI スキーマをそれに応じて更新します。OpenAPI スキーマを簡単に更新するには、アプリをローカルで実行し、`http://0.0.0.0:8000/sub/openapi.json` にアクセスしてウェブページの内容をコピーします。次に、[Swagger Editor](https://editor.swagger.io/) に JSON を貼り付けて、YAML 形式に変換します。また、[openapi.yaml](/.well-known/openapi.yaml) ファイルを [.well-known](/.well-known) フォルダ内の openapi.json ファイルに置き換えることもできます。

- **プラグイン名、説明、使用手順を変更する**：プラグイン名、ユーザー向けの説明、モデルの使用手順を更新します。[main.py](/server/main.py) ファイル内の説明を編集するか、[openapi.yaml](/.well-known/openapi.yaml) ファイルを更新します。前のステップと同じ手順で OpenAPI スキーマを更新してください。

- **ChatGPT が会話から情報を保存できるようにする**：[memory example folder](/examples/memory) の指示に従ってください。


### 認証方法

プラグインへのリクエストの認証には、4つのオプションがあります。

1. **No Authentication**：誰でもあなたのプラグインを追加し、資格情報なしでそのAPIを使用することができます。これは、公開済みのドキュメントやセンシティブでないドキュメントだけを利用している場合に適しています。データのセキュリティは提供されません。この方法を使用する場合、[main.py](/examples/authentication-methods/no-auth/main.py)の内容を[actual main.py file](/server/main.py)にコピーしてください。例のマニフェストは[こちら](/examples/authentication-methods/no-auth/ai-plugin.json)。

2. **HTTP Bearer**：秘密トークンをヘッダーとして使用して、プラグインへのリクエストを承認できます。このオプションには2つのバリエーションがあります。

   - **User Level** (この実装のデフォルト): プラグインをChatGPTに追加する各ユーザーは、プラグインを追加する際にベアラートークンを提供する必要があります。これらのトークンは、[jwt.io](https://jwt.io/)などのツールや方法を使用して生成および配布することができます。この方法は、各ユーザーが共有アクセストークンを入力する必要があるため、より良いセキュリティが提供されます。各ユーザーにユニークなアクセストークンが必要な場合は、[main.py](/server/main.py)ファイルで自分で実装する必要があります。例のマニフェストは[こちら](/examples/authentication-methods/user-http/ai-plugin.json)。

   - **Service Level**: 誰でも、資格情報なしであなたのプラグインを追加し、APIを利用できますが、プラグインを登録するときにベアラトークンを追加する必要があります。プラグインをインストールする際に、ベアラートークンを追加して、ChatGPTからトークンを受け取り、それをホストされたマニフェストファイルに含める必要があります。あなたのトークンは、それを追加したすべてのユーザーを代表して、ChatGPTによるプラグインへのリクエストの承認に使用されます。この方法はユーザーにとって便利ですが、すべてのユーザーが同じトークンを共有し、プラグインをインストールする際にトークンを追加する必要がないため、セキュリティが低くなる可能性があります。例のマニフェストは[こちら](/examples/authentication-methods/service-http/ai-plugin.json)。

3. **OAuth**：ユーザーはOAuthフローを経てプラグインを追加する必要があります。OAuthプロバイダーを使用して、プラグインを追加するユーザーを認証し、APIへのアクセス許可を付与できます。この方法は、信頼できるサードパーティプロバイダーを介してユーザーが認証されるため、最高レベルのセキュリティと制御を提供します。ただし、[main.py](/server/main.py)ファイルでOAuthフローを自分で実装し、マニフェストファイルで必要なパラメータを提供する必要があります。例のマニフェストは[こちら](/examples/authentication-methods/oauth/ai-plugin.json)。

使いたい認証方法を選ぶ前に、各認証方法の利点と欠点を検討して、ユースケースとセキュリティ要件に最も適したものを選んでください。デフォルトと異なる方法（User Level HTTP）を使用する場合は、マニフェストファイル [ここ](/.well-known/ai-plugin.json)を更新してください。

## デプロイ

お好みや要件に応じて、さまざまなクラウドプロバイダにアプリをデプロイできます。ただし、選択したプロバイダに関係なく、アプリ内の2つのファイルを更新する必要があります。[openapi.yaml](/.well-known/openapi.yaml) および [ai-plugin.json](/.well-known/ai-plugin.json)。上記で説明したように、これらのファイルは、それぞれアプリのAPI仕様とAIプラグイン構成を定義します。どちらのファイルのurlフィールドも、デプロイされたアプリのアドレスに合わせて変更する必要があります。

アプリをデプロイする前に、[pyproject.toml](./pyproject.toml)ファイルから使用していない依存関係を削除して、アプリのサイズを減らしパフォーマンスを向上させることを検討してください。選択したベクターデータベースプロバイダに応じて、特定のプロバイダに必要でないパッケージを削除できます。各プロバイダの不要な依存関係を削除する方法については、[`/docs/deployment/removing-unused-dependencies.md`](/docs/deployment/removing-unused-dependencies.md)ファイルのドキュメントを参照してください。

アプリをデプロイしたら、[これらのスクリプト](/scripts)のいずれかを使用して、初期バッチのドキュメントをアップロードするか、`/upsert`エンドポイントを呼び出してみてください。

各プラットフォームの詳細なデプロイ手順は以下のとおりです。

- [Fly.ioへのデプロイ](/docs/deployment/flyio.md)
- [Herokuへのデプロイ](/docs/deployment/heroku.md)
- [その他のデプロイオプション](/docs/deployment/other-options.md)（Azure Container Apps、Google Cloud Run、AWS Elastic Container Serviceなど）

アプリを作成した後、プラグインのマニフェストファイル[ここ](/.well-known/ai-plugin.json)のプラグインURLを変更し、OpenAPIスキーマ[ここ](/.well-known/openapi.yaml)を変更して再デプロイしてください。

## 開発者プラグインのインストール

開発者プラグインをインストールするには、以下の手順に従ってください。

- まず、プラグインをあなたの好きなホスティングプラットフォーム（例えば、Fly.io、Herokuなど）にデプロイし、マニフェストファイルとOpenAPIスキーマのプラグインURLを更新します。

- [ChatGPT](https://chat.openai.com/) にアクセスし、モデルピッカーから "Plugins" を選択します。

- プラグインピッカーから、一番下までスクロールして "Plugin store" をクリックします。

- "Develop your own plugin" に進み、提供された手順に従います。プラグインがデプロイされたドメインを入力する必要があります。

- プラグインで選択した認証タイプに基づいて、手順に従います（例えば、プラグインがService Level HTTPを使用する場合、アクセス・トークンを貼り付ける必要があり、その後、[ai-plugin.json](/.well-known/ai-plugin.json)ファイルに取得した新しいアクセス・トークンを貼り付けてアプリを再デプロイする必要があります）。

- 次に、プラグインを追加する必要があります。再度 "Plugin store" に移動して、「Install an unverified plugin」をクリックします。

- プラグインがデプロイされたドメインを入力する必要がある手順に従います。

- プラグインで選択した認証タイプに基づいて、手順に従います（例えば、プラグインがUser Level HTTPを使用する場合、ベアラトークンを貼り付ける必要があります）。

これらの手順を完了すると、開発者プラグインがChatGPTにインストールされ、使用準備ができます。

## Webhook

ベクトルデータベースに格納されたドキュメントを最新の状態に保つために、[Zapier](https://zapier.com)や[Make](https://www.make.com)のようなツールを使用して、イベントやスケジュールに基づいてプラグインのAPIへの着信ウェブフックを設定することを検討してください。例えば、これにより、ノートを更新したり、メールを受信したりするたびに、新しい情報を同期することができます。また、[Zapier Transfer](https://zapier.com/blog/zapier-transfer-guide/)を使用して、既存のドキュメントのコレクションを一括処理して、ベクトルデータベースにアップロードすることもできます。

これらのツールからカスタムフィールドをプラグインに渡す場合は、「upsert-email」のように、データストアのupsert関数を呼び出す追加のRetrieval Plugin APIエンドポイントを作成することを検討してください。このカスタムエンドポイントは、ウェブフックから特定のフィールドを受け取り、それらを適宜処理するように設計することができます。

着信ウェブフックを設定するには、次の一般的な手順に従ってください。

- ZapierやMakeなどのウェブフックツールを選択し、アカウントを作成します。
- ツールで新しいウェブフックまたはトランスファーを設定し、イベントやスケジュールに基づいてトリガーを設定します。
- ウェブフックのターゲットURLを指定します。これは、あなたのRetrievalプラグインのAPIエンドポイントである必要があります（例えば、`https://your-plugin-url.com/upsert`）。
- ウェブフックペイロードを構成して、必要なデータフィールドを含め、RetrievalプラグインのAPI要件に従ってフォーマットします。
- ウェブフックが正しく機能し、Retrievalプラグインに期待されるようにデータを送信していることを確認するために、ウェブフックをテストします。

ウェブフックを設定した後は、以前に見逃したデータがベクトルデータベースに含まれるようにバックフィルを実行することをお勧めします。

データを連続的に同期するために着信ウェブフックを使用する場合は、これらを設定した後にバックフィルを実行することを忘れないでください。

ZapierやMakeのようなツールを使用するだけでなく、独自のカスタム統合を構築してRetrievalプラグインとデータを同期することもできます。これにより、データフローをより細かく制御し、統合を特定のニーズや要件に合わせてカスタマイズすることができます。

## スクリプト

`scripts`フォルダには、異なるデータソース（zipファイル、JSONファイル、またはJSONLファイル）からテキストドキュメントをバッチでupsertまたは処理するスクリプトが含まれています。これらのスクリプトは、ドキュメントをプレーンテキストに変換し、チャンクに分割した後、プラグインのupsertユーティリティ関数を使用して、ドキュメントとそのメタデータをベクトルデータベースにアップロードします。各スクリプトフォルダには、使用方法と必要なパラメーターについて説明したREADMEファイルがあります。また、[`services.pii_detection`](/services/pii_detection.py)モジュールを使用して、個人を特定できる情報（PII）を言語モデルでスクリーニングし、検出された場合はスキップすることもできます。これは、意図せずに機密性の高いドキュメントをベクトルデータベースにアップロードするのを避けたい場合に役立ちます。さらに、[`services.extract_metadata`](/services/extract_metadata.py)モジュールを使用して、ドキュメントテキストからメタデータを抽出することもできます。これは、ドキュメントメタデータを豊かにする場合に役立ちます。**注意:**データを連続的に同期するための着信ウェブフックを使用する場合は、これらを設定した後にバックフィルを実行して、データを逃さないようにすることを検討してください。

スクリプトは以下の通りです：

-- [`process_jsonl`](/scripts/process_jsonl/): このスクリプトは、JSONL形式のドキュメントのファイルダンプを処理し、それらをベクターデータベースにいくつかのメタデータとともに保存します。JSONLファイルの形式は、改行区切りのJSONファイルで、各行がドキュメントを表す有効なJSONオブジェクトである必要があります。JSONオブジェクトには`text`フィールドが必要で、オプションで他のフィールドをメタデータに追加することができます。カスタムメタデータをJSON文字列として提供し、PIIをスクリーニングし、メタデータを抽出するためのフラグを使うことができます。

- [`process_jsonl`](/scripts/process_jsonl):このスクリプトは、JSONL形式でドキュメントのファイルダンプを処理し、いくつかのメタデータとともにベクトルデータベースに格納します。 JSONLファイルの形式は、各行がドキュメントを表す有効なJSONオブジェクトである改行区切りのJSONファイルである必要があります。 JSONオブジェクトには、`text`フィールドと、必要に応じて他のフィールドが含まれている場合があります。 JSON文字列とPIIのスクリーニングフラグをカスタムメタデータとして提供できます。

- [`process_zip`](/scripts/process_zip): このスクリプトは、zipファイルでドキュメントのファイルダンプを処理し、いくつかのメタデータとともにベクトルデータベースに格納します。 zipファイルの形式は、docx、pdf、txt、md、pptx、またはcsvファイルのフラットなzipファイルフォルダである必要があります。 JSON文字列とPIIのスクリーニングフラグをカスタムメタデータとして提供できます。


## 制限事項

ChatGPT検索プラグインは、セマンティックサーチと検索のための柔軟なソリューションを提供するように設計されていますが、以下の制限があります：

- **キーワード検索の制限**: `text-embedding-ada-002`モデルによって生成される埋め込みは、厳密なキーワードマッチを正確にキャプチャすることができない場合があります。その結果、プラグインは、特定のキーワードに重点を置いたクエリに対して最も関連性の高い結果を返さない可能性があります。 PineconeやWeaviateなどの一部のベクトルデータベースは、ハイブリッド検索を使用し、キーワード検索に対してより優れたパフォーマンスを発揮する場合があります。
- **機密性の高いデータの処理**: プラグインは、機密性の高いデータを自動的に検出またはフィルタリングしません。開発者は、検索プラグインにコンテンツを含めるために必要な承認を持っていることを確認し、コンテンツがデータプライバシーの要件に準拠していることを確認する責任があります。

- **スケーラビリティ**:プラグインのパフォーマンスは、選択したベクトルデータベースプロバイダとデータセットのサイズによって異なる場合があります。一部のプロバイダは、他のプロバイダよりも優れたスケーラビリティとパフォーマンスを提供する場合があります。
- **言語サポート**: 現時点で使用されている`text-embedding-ada-002`モデルは、英語を対象に最適化されています。ただし、多様な言語に対して良好な結果を生成するだけの十分なロバスト性があります。
- **メタデータの抽出**:オプションのメタデータ抽出機能は、ドキュメントテキストから情報を抽出するために言語モデルを使用しています。このプロセスは常に正確でない場合があり、抽出されたメタデータの品質は、ドキュメントの内容と構造によって異なる場合があります。
- **PIIの検出**:オプションのPII検出機能は完全ではなく、個人を特定できる情報のすべてのインスタンスをキャッチできない場合があります。この機能を使用する場合は注意して、特定のユースケースに対してその効果を検証してください。

## 今後の方向性

ChatGPT Retrieval Pluginは、意味論的検索と取得のための柔軟な解決策を提供しますが、さらなる開発の余地があります。新しい機能や改良のためのプルリクエストの提出によるプロジェクトへの貢献をお勧めします。注目すべき貢献にはOpenAIクレジットが与えられる場合があります。

今後の方向性のアイデアには以下があります:

- **より多くのベクトルデータベースプロバイダー**: ChatGPT Retrieval Pluginと別のベクトルデータベースプロバイダーを統合することに興味がある場合は、実装を提出してください。
- **追加スクリプト**: さまざまなデータソースからドキュメントを処理してアップロードするための利用可能なスクリプトの範囲を拡大すると、プラグインはますます多目的になります。
- **ユーザーインターフェース**: ドキュメントの管理やプラグインとの対話を行うためのユーザーインターフェースを開発することで、ユーザーエクスペリエンスが向上できます。
- **ハイブリッド検索 / TF-IDFオプション**：ハイブリッド検索またはTF-IDFインデックスを使用するオプションで、[データストアのアップサート機能](/datastore/datastore.py#L18)を強化することで、キーワードベースのクエリに対するプラグインのパフォーマンスが向上する可能性があります。
- **高度なチャンキング戦略と埋め込み計算**: より洗練されたチャンキング戦略と埋め込み計算の実装、例えば文書のタイトルや要約を埋め込む、文書のチャンクや要約を重み付け平均する、または文書の平均埋め込みを計算するなど、より良い検索結果につながる可能性があります。
- **カスタムメタデータ**: タイトルや他の関連情報など、カスタムメタデータをドキュメントチャンクに追加することで、特定のユースケースで取得された結果が改善される可能性があります。
- **追加のオプションサービス**: ドキュメントの要約や埋め込み前の前処理などの追加サービスを統合することで、プラグインの機能性と取得された結果の品質を向上させることができます。これらのサービスは、スクリプトだけでなく、言語モデルを使用して直接プラグインに統合して実装することができます。

私たちは、ChatGPT Retrieval Pluginの改善と機能拡張を支援するために、コミュニティからの貢献を歓迎します。アイデアや貢献したい機能がある場合は、リポジトリにプルリクエストを提出してください。

## 貢献者

以下の貢献者に、コード/ドキュメントの貢献や、さまざまなベクトルデータベースプロバイダーをChatGPT Retrieval Pluginに統合するサポートを提供していただき、感謝いたします。

- [Pinecone](https://www.pinecone.io/)
  - [acatav](https://github.com/acatav)
  - [gkogan](https://github.com/gkogan)
  - [jamescalam](https://github.com/jamescalam)
- [Weaviate](https://www.semi.technology/)
  - [byronvoorbach](https://github.com/byronvoorbach)
  - [hsm207](https://github.com/hsm207)
  - [sebawita](https://github.com/sebawita)
- [Zilliz](https://zilliz.com/)
  - [filip-halt](https://github.com/filip-halt)
- [Milvus](https://milvus.io/)
  - [filip-halt](https://github.com/filip-halt)
- [Qdrant](https://qdrant.tech/)
  - [kacperlukawski](https://github.com/kacperlukawski)
- [Redis](https://redis.io/)
  - [spartee](https://github.com/spartee)
  - [tylerhutcherson](https://github.com/tylerhutcherson)
