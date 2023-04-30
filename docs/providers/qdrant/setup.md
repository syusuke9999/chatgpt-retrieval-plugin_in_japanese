# Qdrant

[Qdrant](https://qdrant.tech/)は、ドキュメントとベクトル埋め込みを保存できるベクトルデータベースです。自己ホスト版または管理された[Qdrant Cloud](https://cloud.qdrant.io/)ソリューションとして実行できます。設定はどちらのオプションでもほぼ同じで、[Qdrant Cloud](https://cloud.qdrant.io/)が提供するAPIキーを除いています。

**環境変数:**

| 名前                  | 必須 | 説明                                                     | デフォルト              |
|---------------------|----|--------------------------------------------------------|--------------------|
| `DATASTORE`         | はい | データストア名。`qdrant`に設定します。                                |                    |
| `BEARER_TOKEN`      | はい | シークレットトークン                                             |                    |
| `OPENAI_API_KEY`    | はい | OpenAI APIキー                                           |                    |
| `QDRANT_URL`        | はい | QdrantインスタンスのURL                                       | `http://localhost` |
| `QDRANT_PORT`       | 任意 | QdrantのHTTP通信用TCPポート                                   | `6333`             |
| `QDRANT_GRPC_PORT`  | 任意 | QdrantのGRPC通信用TCPポート                                   | `6334`             |
| `QDRANT_API_KEY`    | 任意 | [Qdrant Cloud](https://cloud.qdrant.io/)用のQdrant APIキー |                    |
| `QDRANT_COLLECTION` | 任意 | Qdrantのコレクション名                                         | `document_chunks`  |

## Qdrant Cloud

ホストされた[Qdrant Cloud](https://cloud.qdrant.io/)版の場合は、[Qdrant Cloud UI](https://cloud.qdrant.io/)からQdrantインスタンスのURLとAPIキーを提供してください。

**例:**

```bash
QDRANT_URL="https://YOUR-CLUSTER-URL.aws.cloud.qdrant.io"
QDRANT_API_KEY="<YOUR_QDRANT_CLOUD_CLUSTER_API_KEY>"
```

他のパラメータは任意であり、必要に応じて変更できます。

## 自己ホスト版Qdrantインスタンス

自己ホスト版の場合、Dockerコンテナまたは公式のHelmチャートを使用してデプロイします。必要なパラメータは、QdrantサーバーのURLを指す`QDRANT_URL`だけです。

**例:**

```bash
QDRANT_URL="http://YOUR_HOST.example.com:6333"
```

他のパラメータは任意であり、必要に応じて変更できます。

## Qdrantインテグレーションテストの実行

Qdrantインテグレーションを検証するための一連のインテグレーションテストがあります。実行するには、DockerコンテナでローカルQdrantインスタンスを起動します。

```bash
docker run -p "6333:6333" -p "6334:6334" qdrant/qdrant:v1.0.3
```

次に、以下のコマンドでテストスイートを実行します。

```bash
pytest ./tests/datastore/providers/qdrant/test_qdrant_datastore.py
```
