# Zilliz

[Zilliz](https://zilliz.com)は、ビリオンスケールに対応したマネージドクラウドネイティブベクターデータベースです。Zillizは以下のような多くの主要な機能を提供しています。

- 複数のインデックスアルゴリズム
- 複数の距離指標
- スカラーフィルタリング
- タイムトラベル検索
- ロールバックとスナップショット
- 完全なRBAC
- 99.9%の稼働率
- ストレージとコンピューティングの分離
- マルチ言語SDK

詳細については[こちら](https://zilliz.com)をご覧ください。

**セルフホスト vs SaaS**

ZillizはSaaSデータベースであり、オープンソースのソリューションであるMilvusも提供しています。どちらのオプションもビリオンスケールでの高速検索を提供しますが、Zillizはデータ管理を代行します。自動的にコンピューティングとストレージリソースをスケーリングし、データに最適なインデックスを作成します。比較は[こちら](https://zilliz.com/doc/about_zilliz_cloud)でご覧いただけます。

## データベースのデプロイ

Zilliz Cloudは簡単な手順でデプロイできます。まず、[こちら](https://cloud.zilliz.com/signup)でアカウントを作成します。アカウントが設定されたら、[こちら](https://zilliz.com/doc/quick_start)のガイドに従ってデータベースを設定し、このアプリケーションに必要なパラメータを取得します。

環境変数:

| 名前                         | 必須 | 説明                                      |
|----------------------------|----|-----------------------------------------|
| `DATASTORE`                | はい | データストア名。`zilliz`に設定します。                 |
| `BEARER_TOKEN`             | はい | あなたのシークレットトークン                          |
| `OPENAI_API_KEY`           | はい | あなたのOpenAI APIキー                        |
| `ZILLIZ_COLLECTION`        | 任意 | Zillizコレクション名。デフォルトではランダムなUUIDになります。    |
| `ZILLIZ_URI`               | はい | ZillizインスタンスのURI                        |
| `ZILLIZ_USER`              | はい | Zillizのユーザー名                            |
| `ZILLIZ_PASSWORD`          | はい | Zillizのパスワード                            |
| `ZILLIZ_CONSISTENCY_LEVEL` | 任意 | コレクションのデータ整合性レベル。デフォルトでは`Bounded`になります。 |

## Zillizインテグレーションテストの実行

Zillizインテグレーションを検証するための統合テストスイートが用意されています。テストを実行するには、Zillizデータベースを作成し、環境変数を更新します。

次に、以下のコマンドでテストスイートを起動します。

```bash
pytest ./tests/datastore/providers/zilliz/test_zilliz_datastore.py
```
