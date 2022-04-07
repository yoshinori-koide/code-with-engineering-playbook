# DocFxドキュメントWebサイトをAzure Webサイトに自動的にデプロイする

記事 「[DocFxとコンパニオンツールを使用してドキュメントWebサイトを生成する](using-docfx-and-tools.md)」では、DocFxを使用してドキュメントWebサイトのコンテンツを生成するプロセスについて説明しています。このドキュメントでは、Azure Webサイトをセットアップしてコンテンツをホストし、AzureDevOpsのパイプラインを使用してコンテンツへのデプロイを自動化する方法について説明します。

DocFx生成のクイックセットアップ用に提供されている[QuickStartサンプル](https://github.com/mtirionMSFT/DocFxQuickStart)には、このドキュメントで説明されているファイルも含まれています。特に **.pipelines**と**インフラストラクチャ**フォルダ。

クイックスタートフォルダを使用する場合は、次の手順に従うことができます。**インフラストラクチャ** フォルダーには、Azure環境でWebサイトを作成するためのTerraformファイルがあります。箱から出して、スクリプトはドキュメントコンテンツを展開できるWebサイトを作成します。
The following steps can be followed when using the Quick Start folder. In the **infrastructure** folder you can find the Terraform files to create the website in an Azure environment. Out of the box, the script will create a website where the documentation content can be deployed to.

## 1. Terraformをインストールします

[Chocolatey](https://chocolatey.org/)などのツールを使用してTerraformをインストールできます。

```shell
choco install terraform
```

## 2. 適切な変数を設定します

> **重要：** **app_name**、**rg_name**、および**rg_location**変数の値を必ず変更してください。app_name値はazurewebsites.netによって追加され、一意である必要があります。そうしないと、スクリプトが失敗し、Webサイトを作成できなくなります。

クイックスタートでは、認証は無効になっています。これを有効にする場合は、Azure ADで *Application* を作成し、*client ID* を持っていることを確認してください。このクライアントIDは、 *variables.tf* の **client_id**変数の値として設定する必要があります。*main.tf* で、 *app-service* の認証設定のコメントを解除してください。詳細については、「[AzureAD認証の構成-AzureAppService](https://docs.microsoft.com/en-us/azure/app-service/configure-authentication-provider-aad)」を参照してください。

ドキュメントWebサイトにSSL証明書を使用してカスタムドメインを設定する場合は、いくつかの追加手順を実行する必要があります。*Key Vault*を作成し、そこに証明書を保存する必要があります。次のステップは、コメントを外して、*variables.tf*の値を設定することです。また、*main.tf*で必要な手順のコメントを解除する必要があります。すべてコメントボックスで示されます。詳細については、「[AzureAppServiceにTLS/SSL証明書を追加する](https://docs.microsoft.com/en-us/azure/app-service/configure-ssl-certificate)」を参照してください。

SSL証明書、カスタムドメイン、およびAzure App Serviceに関する追加情報は、次の段落に記載されています。これに精通している場合、または必要ない場合は、[ステップ3](#3-deploy-azure-resources-from-your-local-machine)に進んでください。

### SSL証明書

カスタムドメイン名と証明書を使用してWebサイトを保護するには、記事「[AzureAppServiceでのTLS/SSL証明書の追加](https://docs.microsoft.com/en-us/azure/app-service/configure-ssl-certificate)」を参照してください。この記事には、証明書を取得する方法と証明書の要件についての説明も含まれています。通常、顧客のIT部門から証明書を取得します。プロセスをテストするために開発証明書から始めたい場合は、自分で作成することができます。以下のスクリプトを使用して、PowerShellでこれを行うことができます。
置換するのは：

* **[YOUR DOMAIN]** をあなたが登録したいドメインとする。例えば `docs.somewhere.com`
* **[PASSWORD]** を証明書のパスワードとする。Key Vaultに証明書をアップロードするには、パスワードが必要です。そのステップでこのパスワードが必要になります。
* **[FILENAME]** は証明書の出力ファイル名とする。ここに、マシンの保存場所にパスを挿入することもできます。

このスクリプトは、PowerShellスクリプトファイル（.ps1）に保存できます。

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation cert:\currentuser\my -Subject "cn=[YOUR DOMAIN]" -DnsName "[YOUR DOMAIN]"
$pwd = ConvertTo-SecureString -String '[PASSWORD]' -Force -AsPlainText
$path = 'cert:\currentuser\my\' + $cert.thumbprint
Export-PfxCertificate -cert $path -FilePath [FILENAME].pfx -Password $pwd
```

証明書は、共通のKeyVaultに保存する必要があります。Key Vaultの左側の`Settings > Certificates`メニューに移動し、`Generate/Import`をクリックします。これらの詳細を提供します。

* 証明書の作成方法：`Import`

* 証明書名：例 `ssl-certificate`

* 証明書ファイルのアップロード：このためにディスク上のファイルを選択します。

* パスワード：これは、前に参照した [PASSWORD] です。

### カスタムドメイン登録

カスタムドメインを使用するには、いくつかのことを行う必要があります。Azureポータルでのプロセスについては、[チュートリアル：既存のカスタムDNS名をAzure App Service](https://docs.microsoft.com/en-us/azure/app-service/app-service-web-tutorial-custom-domain)にマップするの記事で説明されています。重要な部分は、「[ドメイン検証IDの取得](https://docs.microsoft.com/en-us/azure/app-service/app-service-web-tutorial-custom-domain#get-a-domain-verification-id)」というヘッダーの下に説明されています。このIDは、DNS記述にTXTレコードとして登録する必要があります。

知っておくべき重要なことは、この`Custom Domain Verification ID`は同じAzureサブスクリプション内のすべてのWebリソースで同じであるということです。[このStackOverflowの問題](https://stackoverflow.com/questions/64309200/is-the-custom-domain-verification-shared-across-an-azure-subscription)を参照してください。つまり、このIDは、1つのAzureサブスクリプションに対して1回だけ登録する必要があります。これにより、スクリプトを介してカスタムドメインを使用してApp Serviceを（再）作成できます。

### Microsoft Azure App ServiceのGet-permissionsを追加する

Azure App Serviceは、証明書を取得するためにKeyVaultにアクセスする必要があります。これは、最初の実行だけでなく、証明書がKeyVaultで更新されるときにも必要です。この目的のために、Azure App Serviceは、AppServiceリソースが提供するIDを使用してKeyVaultにアクセスします。このIDは、サービスプリンシパル名 **abfa0a7c-a6b6-4736-8310-5855508787cd** または **Microsoft Azure App Service** で見つけることができ、タイプは **Application** です。このIDは、すべてのAzureサブスクリプションで同じです。シークレットと証明書の取得許可が必要です。詳細については、この記事「[KeyVaultから証明書をインポートする](https://docs.microsoft.com/en-us/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)」を参照してください。

### カスタムドメインとSSL証明書をApp Serviceに追加します

SSL証明書を取得し、説明したように完全なDNS登録が完了したら、クイックスタートフォルダーからTerraformスクリプトのコードのコメントを解除して、これをAppServiceに添付できます。このスクリプトでは、共通のKey Vaultで証明書を参照し、それをカスタムホスト名バインディングで使用する必要があります。カスタムホスト名は、スクリプトでも割り当てられます。SSL証明書を使用している場合は、`ssl_state`の設定を`SniEnabled`とすることが必要です。これで、カスタムドメインを使用した認証済みWebサイトの作成が自動化されました。

## 3. ローカルマシンからAzureリソースをデプロイします

コマンドプロンプトを開きます。コマンドを実行するには、Azureサブスクリプションに接続する必要があります。これは、 [Azure Cli](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-windows?tabs=azure-cli)を使用して実行できます。
```shell
az login
```

これにより、Webブラウザを使用してアカウントにログインします。次のコマンドを使用して、接続されているサブスクリプションを確認できます。

```shell
az account show
```

別のサブスクリプションに変更する必要がある場合は、次のコマンドを使用して[id]をサブスクリプションのIDに置き換え、以下を選択します。

```shell
az account set --subscription [id]
```

これが完了したら、次のコマンドを実行して初期化します。

```shell
terraform init
```

これで、コマンドを実行して、スクリプトの実行内容を計画できます。terraformスクリプトに変更が加えられるたびに、このコマンドを実行します。

```shell
terraform plan
```

表示された結果を調べます。それが期待どおりの場合は、次のコマンドを使用して次の変更を適用します。

```shell
terraform apply
```

承認を求められたら、「はい」と入力してEnterキーを押します。applyコマンドに *-auto-approve* フラグを追加することもできます。

次の手順で説明するように、Terraformを使用した展開は、クイックスタートフォルダーからのパイプラインに含まれていません。これは、追加の構成が必要になるためです。しかしもちろん、それはいつでも追加できます。

## 4. パイプラインからWebサイトを展開します

リソースを作成してデプロイする最良の方法は、パイプラインでこれを自動的に行うことです。この目的のために、**.pipelines/documentation.yml** パイプラインが提供されています。このパイプラインは、AzureDevOps環境用に構築されています。パイプラインを作成し、このYAMLファイルを参照します。

> **重要：** クイックスタートフォルダーには、IISまたはAzureAppServiceへの展開に必要なweb.configが含まれています。これにより、検索リクエストにjsonファイルを使用できるようになります。これが適切に行われていない場合、テキストを検索しても何も返されず、結果として404が内部に表示されます。

デプロイ先のAzureサブスクリプションに接続するには、DevOps環境でサービス接続を作成する必要があります。

> **重要：** 変数 **AzureConnectionName** をサービス接続の名前に設定し、**AzureAppServiceName** を *infrastructure/variables.tf* で決定した名前に設定します。

クイックスタートフォルダーでは、パイプラインが `master`トリガーとして使用します。これは、マスターに対してプッシュが実行されるとパイプラインがトリガーされることを意味します。おそらくこれを別のブランチに変更します。
