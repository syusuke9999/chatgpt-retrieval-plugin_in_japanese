# Milvus

[Milvus](https://milvus.io/) は、数十億のベクトルにスケールするオープンソースのクラウドネイティブベクターデータベースです。Zillizのオープンソース版です。これには以下の機能があります。

- さまざまなインデックスアルゴリズムと距離指標
- スカラーフィルタリングとタイムトラベル検索
- ロールバックとスナップショット
- マルチ言語SDK
- ストレージと計算の分離
- クラウドのスケーラビリティ
- マルチ言語サポートを備えた開発者向けコミュニティ

詳細については、[Github](https://github.com/milvus-io/milvus) をご覧ください。

## データベースのデプロイ

Milvusは、Docker Compose、Helm、K8のオペレーター、またはAnsibleを使用してデプロイおよび管理できます。[こちら](https://milvus.io/docs) の手順に従って始めてください。

**環境変数:**

| 名前                         | 必須 | 説明                                                                                                                     |
|----------------------------|----|------------------------------------------------------------------------------------------------------------------------|
| `DATASTORE`                | はい | データストア名を `milvus` に設定します                                                                                               |
| `BEARER_TOKEN`             | はい | あなたのベアラートークン                                                                                                           |
| `OPENAI_API_KEY`           | はい | あなたのOpenAI APIキー                                                                                                       |
| `MILVUS_COLLECTION`        | 任意 | Milvusコレクション名（デフォルトではランダムなUUID）                                                                                        |
| `MILVUS_HOST`              | 任意 | MilvusホストIP（デフォルトは `localhost`）                                                                                        |
| `MILVUS_PORT`              | 任意 | Milvusポート（デフォルトは `19530`）                                                                                              |
| `MILVUS_USER`              | 任意 | RBACが有効な場合のMilvusユーザー名（デフォルトは `None`）                                                                                  |
| `MILVUS_PASSWORD`          | 任意 | 必要に応じてMilvusパスワード（デフォルトは `None`）                                                                                       |
| `MILVUS_INDEX_PARAMS`      | 任意 | コレクションのカスタムインデックスオプション（デフォルトは `{"metric_type": "IP", "index_type": "HNSW", "params": {"M": 8, "efConstruction": 64}}`） |
| `MILVUS_SEARCH_PARAMS`     | 任意 | コレクションのカスタム検索オプション（デフォルトは `{"metric_type": "IP", "params": {"ef": 10}}`）                                               |
| `MILVUS_CONSISTENCY_LEVEL` | 任意 | コレクションのデータ整合性レベル（デフォルトは `Bounded`）                                                                                     |

## Milvus統合テストの実行

Milvus統合を検証するための統合テストスイートが用意されています。テストを実行するには、examplesフォルダーにあるmilvus docker composeを実行してください。

次に、このコマンドでテストスイートを起動します。

```bash
pytest ./tests/datastore/providers/milvus/test_milvus_datastore.py
```
