# LlamaIndex

[LlamaIndex](https://github.com/jerryjliu/llama_index)は、LLMを外部データと接続するための中央インターフェースです。
これは、ChatGPTを使用するための構造化および非構造化データに対するインメモリインデックスのスイートを提供します。
標準のベクターデータベースとは異なり、LlamaIndexはさまざまなユースケースに最適化されたインデックス戦略（例：ツリー、キーワードテーブル、ナレッジグラフ）をサポートしています。
軽量で使いやすく、追加のデプロイメントは必要ありません。
環境変数をいくつか指定するだけでよいです（オプションで既存の保存されたIndex jsonファイルを指定することもできます）。
ただし、クエリ内のメタデータフィルタはまだサポートされていません。

## セットアップ
現在、LlamaIndexは追加のデプロイメントが不要であり、
Retrieval Pluginの一部として実行されます。
ChatGPTと外部データを使ったクイックプロトタイピングに最適で、非常に簡単にセットアップできます。

**検索アプリ環境変数**

| 名前               | 必須 | 説明                        |
|------------------|----|---------------------------|
| `DATASTORE`      | はい | データストア名。これを `llama`に設定します |
| `BEARER_TOKEN`   | はい | あなたのシークレットトークン            |
| `OPENAI_API_KEY` | はい | あなたのOpenAI APIキー          |

**Llamaデータストア環境変数**

| 名前                             | 必須 | 説明                             | デフォルト         |
|--------------------------------|----|--------------------------------|---------------|
| `LLAMA_INDEX_TYPE`             | 任意 | インデックスタイプ（詳細については以下を参照）        | `simple_dict` |
| `LLAMA_INDEX_JSON_PATH`        | 任意 | 保存されたIndex jsonファイルへのパス        | なし            |
| `LLAMA_QUERY_KWARGS_JSON_PATH` | 任意 | 保存されたquery kwargs jsonファイルへのパス | なし            |
| `LLAMA_RESPONSE_MODE`          | 任意 | クエリのレスポンスモード                   | `no_text`     | 


**異なるインデックスタイプ**
デフォルトでは、`GPTSimpleVectorIndex`を使用して文書のチャンクをメモリ内に保存し、
埋め込みの類似性によって上位kのノードを取得します。
異なるインデックスタイプは、異なるデータとクエリのユースケースに最適化されています。
詳細については、[How Each Index Works](https://gpt-index.readthedocs.io/en/latest/guides/primer/index_guide.html)を参照してください。
インデックスタイプは`LLAMA_INDEX_TYPE`を使って設定できます。許可されるインデックスタイプ識別子の完全なリストについては、[こちら](https://gpt-index.readthedocs.io/en/latest/reference/indices/composability_query.html#gpt_index.data_structs.struct_type.IndexStructType)を参照してください。

[readthedocs](https://gpt-index.readthedocs.io/en/latest/)で詳細を読むか、
[discord](https://discord.com/invite/dGcwcsnxhU)でコミュニティに参加してください。

## テストの実行
このコマンドでテストスイートを起動できます：

```bash
pytest ./tests/datastore/providers/llama/test_llama_datastore.py
```