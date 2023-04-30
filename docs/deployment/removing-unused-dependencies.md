# 未使用の依存関係の削除

アプリをデプロイする前に、アプリのサイズを削減し、パフォーマンスを向上させるために、[pyproject.toml](/pyproject.toml)ファイルから未使用の依存関係を削除することができます。選択したベクターデータベースプロバイダーに応じて、特定のプロバイダーに必要のないパッケージを削除できます。

各ベクターデータベースプロバイダーで削除できるパッケージは以下の通りです。

- **Pinecone:** `weaviate-client`、`pymilvus`、`qdrant-client`、`redis`、`llama-index`を削除します。
- **Weaviate:** `pinecone-client`、`pymilvus`、`qdrant-client`、`redis`、`llama-index`を削除します。
- **Zilliz:** `pinecone-client`、`weaviate-client`、`qdrant-client`、`redis`、`llama-index`を削除します。
- **Milvus:** `pinecone-client`、`weaviate-client`、`qdrant-client`、`redis`、`llama-index`を削除します。
- **Qdrant:** `pinecone-client`、`weaviate-client`、`pymilvus`、`redis`、`llama-index`を削除します。
- **Redis:** `pinecone-client`、`weaviate-client`、`pymilvus`、`qdrant-client`、`llama-index`を削除します。
- **LlamaIndex:** `pinecone-client`、`weaviate-client`、`pymilvus`、`qdrant-client`、`redis`を削除します。

`pyproject.toml`ファイルから不要なパッケージを削除した後、`poetry lock`と`poetry install`を手動で実行する必要はありません。提供されたDockerfileは、`poetry export`コマンドで生成された`requirements.txt`ファイルを使用して、必要な依存関係をインストールします。
