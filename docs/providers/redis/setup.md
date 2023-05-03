# Redis

[Redis](https://redis.com/solutions/use-cases/vector-database/)は、日常のアプリケーションやAI/MLワークロードに対応したさまざまなユースケースをサポートするリアルタイムデータプラットフォームです。[Redis Stack dockerコンテナ](/examples/docker/redis/docker-compose.yml)を使ってRedisデータベースを作成し、低レイテンシのベクターエンジンとしてRedisを使用します。ホスト/管理ソリューションとしては、[Redis Cloud](https://app.redislabs.com/#/)を試してみてください。Redisをベクターデータベースとして使用するその他の役立つ例は[こちら](https://github.com/RedisVentures/redis-ai-resources)を参照してください。

- データベースは、**RediSearchモジュール(>=v2.6)とRedisJSON**が必要で、上記の自己ホスト型docker composeに含まれています。
- Redis dockerイメージを使ってアプリを実行します：[このディレクトリ](/examples/docker/redis/)で`docker compose up -d`を実行します。
- アプリは、最初の実行時にRedisベクター検索インデックスを自動的に作成します。オプションで、特定の名前でカスタムインデックスを作成し、環境変数として設定します（下記参照）。
- より多機能な検索機能を有効にするには、[こちら](/datastore/providers/redis_datastore.py)のドキュメントスキーマを調整してください。

**環境変数:**

| 名前                      | 必須 | 説明                                                                                                             | デフォルト       |
|-------------------------|----|----------------------------------------------------------------------------------------------------------------|-------------|
| `DATASTORE`             | はい | データストア名。`redis`に設定します。                                                                                         |             |
| `BEARER_TOKEN`          | はい | シークレットトークン                                                                                                     |             |
| `OPENAI_API_KEY`        | はい | OpenAI APIキー                                                                                                   |             |
| `REDIS_HOST`            | 任意 | RedisホストURL                                                                                                    | `localhost` |
| `REDIS_PORT`            | 任意 | Redisポート                                                                                                       | `6379`      |
| `REDIS_PASSWORD`        | 任意 | Redisパスワード                                                                                                     | なし          |
| `REDIS_INDEX_NAME`      | 任意 | Redisベクターインデックス名                                                                                               | `index`     |
| `REDIS_DOC_PREFIX`      | 任意 | インデックスのRedisキープレフィックス                                                                                          | `doc`       |
| `REDIS_DISTANCE_METRIC` | 任意 | ベクター類似性距離メトリック                                                                                                 | `COSINE`    |
| `REDIS_INDEX_TYPE`      | 任意 | [ベクターインデックスアルゴリズムタイプ](https://redis.io/docs/stack/search/reference/vectors/#creation-attributes-per-algorithm) | `FLAT`      |


## Redis Datastoreの開発とテスト
Redis Datastoreへの変更をテストするには以下のコマンドを実行します：

```bash
# Redis stack dockerイメージを実行
docker run -it --rm -p 6379:6379 redis/redis-stack-server:latest
```
    
```bash
# Redisデータストアのテストを実行
poetry run pytest -s ./tests/datastore/providers/redis/test_redis_datastore.py
```