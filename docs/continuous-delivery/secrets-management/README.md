# シークレットマネジメント

シークレット管理とは、構成設定やその他の機密データを保護する方法を指します。これらのデータは、公開された場合、リソースへの不正アクセスを許可します。シークレットの例としては、ユーザー名、パスワード、APIキー、SASトークンなどがあります。

作業中のリポジトリは、最初は非公開であっても、いつでも公開され、秘密を保護できると想定する必要があります。

## 一般的方法

一般的なアプローチは、リポジトリにチェックインされていない個別の構成ファイルにシークレットを保持することです。ファイルを[.gitignore](https://git-scm.com/docs/gitignore)に追加して、チェックインされないようにします。

各開発者は、ファイルの独自のローカルバージョンを維持するか、必要に応じて、Teamsチャットなどのプライベートチャネルを介してファイルを配布します。

本番システムでは、Azureを想定して、実行中のプロセスの環境にシークレットを作成します。これは、リソースの[アプリケーション設定]セクションを手動で編集することで実行できますが、Azure CLIを使用して同じことを行うスクリプトは、時間を節約できる便利なユーティリティです。詳細については、[az webapp config appsettings](https://docs.microsoft.com/en-us/cli/azure/webapp/config/appsettings?view=azure-cli-latest)を参照してください。

実行する環境ごとに個別のシークレット構成を維持することをお勧めします。例：dev、test、prod、localなど

[ブランチごとのシークレットのレシピ](./recipes/azure-devops/secrets-per-branch.md)では、環境ごとに個別のシークレット構成を管理する簡単な方法について説明しています。

> 注：シークレットが機能ブランチにプッシュされただけでマージされなかったとしても、それはgit履歴の一部です。[これらの手順](https://help.github.com/en/github/authenticating-to-github/removing-sensitive-data-from-a-repository)に従って、機密データを削除したり、リポジトリに追加されたキーやその他の機密情報を再生成したりします。キーまたはシークレットがコードベースに入った場合は、キー/シークレットを回転させてアクティブでなくなるようにします

## 秘密を守る

私たちの秘密を保護するために取られた注意は、私たちがそれらを取得して保存する方法だけでなく、私たちがそれらを使用する方法にも適用されます。

- **秘密を記録しない**
- レポートに入れない
- URL、フォームの一部として、またはその秘密を必要とするサービスにリクエストを行う以外の方法で、他のアプリケーションに送信しない

## 強化されたセキュリティアプリケーション

以下に概説する手法は、幅広い言語に*優れた*セキュリティと共通のパターンを提供します。Azureは、アプリが実行されるまでアプリケーション設定（環境）を暗号化しておくという事実に依存しています。

実行時にメモリ内のプレーンテキストにシークレットが存在することを*妨げません*。特に、ガベージコレクションされた言語の場合、これらの値は変数の存続期間より長く存在する可能性があり、プロセスのメモリダンプをデバッグするときに表示される可能性があります。

> セキュリティ要件が強化されたアプリケーションで作業している場合は、アプリケーションの存続期間を通じてシークレットの暗号化を維持するために、追加の手法を使用することを検討する必要があります。

暗号化キーは常に定期的にローテーションしてください。

## シークレット管理のテクニック

これらの手法により、シークレットのロードが開発者に対して透過的になります。

### C#/.NET

appSettings要素の[`file`](https://docs.microsoft.com/en-us/dotnet/framework/configure-apps/file-schema/appsettings/appsettings-element-for-configuration)属性を使用して、ローカルファイルからシークレットをロードします。

``` XML
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <appSettings file="..\..\secrets.config">
  …
  </appSettings>
  <startup>
      <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.6.1" />
  </startup>
  …
</configuration>
```

アクセスの秘密：

```C#
static void Main(string[] args)
{
    String mySecret = System.Configuration.ConfigurationManager.AppSettings["mySecret"];
}
```

Azureで実行している場合、ConfigurationManagerはこれらの設定をプロセス環境から読み込みます。シークレットファイルをサーバーにアップロードしたり、コードを変更したりする必要はありません。

### Node

シークレットを環境変数または`.env`ファイルに保存します

```bash
$ cat .env
MY_SECRET=mySecret
```

[dotenv](https://www.npmjs.com/package/dotenv)パッケージを使用して、環境変数をロードしてアクセスします

```node
require('dotenv').config()
let mySecret = process.env("MY_SECRET")
```

### Python

シークレットを環境変数または`.env`ファイルに保存します

```bash
$ cat .env
MY_SECRET=mySecret
```

[dotenv](https://pypi.org/project/python-dotenv/)パッケージを使用して、環境変数をロードしてアクセスします

```Python
import os
from dotenv import load_dotenv


load_dotenv()
my_secret = os.getenv('MY_SECRET')
```

環境変数を読み取るためのもう1つの優れたライブラリは `environs`

```Python
from environs import Env


env = Env()
env.read_env()
my_secret = os.environ["MY_SECRET"]
```

### データブリックス

Databricksには、クレデンシャルを取得するための安全な方法としてdbutilsを使用し、Databricksで実行されているノートブック内でそれらを公開しないオプションがあります。

次の手順は、新しいシークレットを作成し、Databricksのノートブック内でそれらを利用するための明確な経路を示しています。

1. ローカルマシンに[DatabricksCLIをインストールして構成](https://docs.databricks.com/user-guide/dev-tools/databricks-cli.html#set-up-the-cli)します
2. [Databricksパーソナルアクセストークンを取得する](https://docs.databricks.com/api/latest/authentication.html#token-management)
3. [シークレットのスコープを作成する](https://docs.azuredatabricks.net/user-guide/secrets/secret-scopes.html#create-a-databricks-backed-secret-scope)
4. [シークレットを作成する](https://docs.azuredatabricks.net/user-guide/secrets/secrets.html)

### 検証

自動クレデンシャルスキャンは、プログラミング言語に関係なく、コードに対して実行できます。詳細は[こちら](../../continuous-integration/dev-sec-ops/secret-management/credential_scanning.md)
