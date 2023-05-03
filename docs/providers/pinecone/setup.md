# Pinecone

[Pinecone](https://www.pinecone.io)は、速度・規模・そして早期のプロダクションへの出荷を目的としたマネージドベクターデータベースです。Pineconeをベクターデータベースプロバイダとして使用するには、まず[アカウントにサインアップ](https://app.pinecone.io/)してAPIキーを取得してください。APIキーはダッシュボードのサイドバーにある「APIキー」セクションからアクセスできます。
Pineconeはハイブリッド検索にも対応しており、執筆時点ではSPLADEスパースベクターをネイティブでサポートしている唯一のデータストアです。

Pineconeフレーバーの取得プラグインに関する完全なJupyterノートブックのウォークスルーは[こちら](https://github.com/openai/chatgpt-retrieval-plugin/blob/main/examples/providers/pinecone/semantic-search.ipynb)で見つけることができます。また、[ビデオウォークスルーもこちら](https://youtu.be/hpePPqKxNq8)にあります。

アプリを初めて実行すると、Pineconeインデックスが自動的に作成されます。インデックスに名前を付けて環境変数として設定するだけです。

**環境変数:**

| 名前                     | 必須 | 説明                                                                                                 |
|------------------------|----|----------------------------------------------------------------------------------------------------|
| `DATASTORE`            | はい | データストア名。これを`pinecone`に設定します。                                                                       |
| `BEARER_TOKEN`         | はい | APIへのリクエストの認証に使用する秘密トークン。                                                                          |
| `OPENAI_API_KEY`       | はい | `text-embedding-ada-002`モデルで埋め込みを生成するためのOpenAI APIキー。                                              |
| `PINECONE_API_KEY`     | はい | [Pineconeコンソール](https://app.pinecone.io/)で見つけることができるPinecone APIキー。                                |
| `PINECONE_ENVIRONMENT` | はい | [Pineconeコンソール](https://app.pinecone.io/)で見つけることができるPinecone環境。例：`us-west1-gcp`、`us-east-1-aws`など。 |
| `PINECONE_INDEX`       | はい | 選択したPineconeインデックス名。**注意:** インデックス名は、英数字の小文字または'-'で構成されている必要があります。                                 |

独自のインデックスをカスタム設定で作成したい場合は、Pinecone SDK、API、またはWebインターフェイスを使用して作成できます（[ドキュメント参照](https://docs.pinecone.io/docs/manage-indexes)）。埋め込みには1536の次元を使用し、メタデータのテキストフィールドのインデックス作成を避けてください。これにより、パフォーマンスが大幅に低下します。

```python
# Pinecone SDKでインデックスを作成 - インデックスを手動で作成する場合にのみ使用します。

import os, pinecone

pinecone.init(api_key=os.environ['PINECONE_API_KEY'],
              environment=os.environ['PINECONE_ENVIRONMENT'])

pinecone.create_index(name=os.environ['PINECONE_INDEX'],
                      dimension=1536,
                      metric='cosine',
                      metadata_config={
                          "indexed": ['source', 'source_id', 'url', 'created_at', 'author', 'document_id']})

