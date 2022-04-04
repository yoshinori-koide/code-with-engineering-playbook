# カスタムコネクタテスト

※ オリジナル: https://microsoft.github.io/code-with-engineering-playbook/automated-testing/unit-testing/custom-connector/

Power Platformにデータを配置するためのカスタムコネクタを開発する場合、従うことができるいくつかの戦略があります。

## ユニットテスト

コードが正しく機能していることを確認するために、カスタムコネクタの開発中に実行できる検証がいくつかあります。

主なものは2つあります。

- コネクタが定義されているOpenAPIスキーマを検証します。
- スキーマに、認定されたコネクタプロセスに必要なすべての情報も含まれているかどうかを検証します。

（後者はオプションですが、認定コネクタとして公開する場合に必要です）。

OpenAPIスキーマの検証に役立つツールがいくつかあります。それらのリストは、この[リンク](https://openapi.tools/#description-validators)から入手できます。推奨されるツールは[swagger-cli](https://github.com/APIDevTools/swagger-cli)です。

一方、作成しているカスタムコネクタが認定コネクタになるのに正しいかどうかを検証するには、[paconn-cli](https://github.com/microsoft/PowerPlatformConnectors/tree/dev/tools/paconn-cli)を使用します。これは、カスタムコネクタ定義から欠落している情報を表示するvalidateコマンドがあるためです。
