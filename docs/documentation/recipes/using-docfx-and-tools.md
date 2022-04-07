# DocFxおよびコンパニオンツールを使用してドキュメントWebサイトを生成する

すべてのドキュメントがMarkdownファイルからのものであり、コメントがコードからのものであるWebサイトを簡単に作成する方法が必要な場合は、DocFxを使用できます。[DocFx](https://dotnet.github.io/docfx/)によって生成されたWebサイトには、高速検索機能も含まれています。DocFxソリューションにはいくつかのギャップがありますが、それらのギャップを埋めるのに役立つコンパニオンツールを提供しています。ソリューションの詳細については、ブログ投稿「[DocFxおよびコンパニオンツールを使用したプロジェクトでの高品質のドキュメントの提供](https://mtirion.medium.com/providing-quality-documentation-in-your-project-with-docfx-and-companion-tools-76aed42b1ddd)」も参照してください。

## 前提条件

このドキュメントを続けるには、最初に <https://github.com/mtirionMSFT/DocFxQuickStart> からサンプルのクローンを作成するのが最適です。QuickStartフォルダーの内容を独自のリポジトリーのルートにコピーして、独自の環境で開始します。

## クイックスタート

> **TLDR;**
>
> 内容と方法を読まずにAzureDevOpsとAzureAppServiceの使用をすぐに開始したい場合は、次の手順に従ってください。
>
> 1. **Azure DevOps:** まだ持っていない場合は、Azure DevOpsでプロジェクトを作成し、[Azure環境へのサービス接続を作成します](https://docs.microsoft.com/en-us/azure/devops/pipelines/library/connect-to-azure?view=azure-devops)。リポジトリのクローンを作成します。
> 2. **QuickStartフォルダ:** <https://github.com/mtirionMSFT/DocFxQuickStart> にあるリポジトリーのQuickStartフォルダーの内容をリポジトリーのルートにコピーします。
> 3. **Azure:** Azure：ドキュメントWebサイトのリソースを作成する必要があるAzure環境にリソースグループを作成します。
> 4. **Azureリソースの作成:** *infrastructure/variables.tf* のデフォルト値を置き換え、[手順3のコマンド - ローカルマシンからAzureリソースをデプロイ](deploy-docfx-azure-website.md#3-Running-the-commands-locally)を実行して、Azureリソースを作成します。
> 5. **パイプライン:** *.pipelines/documentation.yml*のデフォルト値を書き換え変更をコミットして、リポジトリのコンテンツを（おそらくPRを介して）ブランチにプッシュします。
>    これで、*.pipelines/documentation.yml* を使用するパイプラインをAzureDevOpsプロジェクトに作成して実行できます。
>

## ドキュメントとプロジェクトのフォルダ構造

最も簡単なのは、ドキュメントとコードが共存する[モノリポジトリ](https://mtirion.medium.com/monorepo-for-beginners-45d5059ab40e)を操作することです。それがあなたの状況に当てはまらないが、それでも複数のリポジトリを1つのドキュメントWebサイトに結合したい場合は、情報を結合できるように、最初にすべてのリポジトリのクローンを作成する必要があります。このレシピでは、モノレポが使用されていると仮定します。

以下の手順では、このコンテンツ構造からのドキュメントWebサイトの生成を検討します。

```xaml
├── .pipelines             // Azure DevOps pipeline for automatic generation and deployment
│
├── docs                     // all documents
│   ├── .attachments  // all images and other attachments used by documents
│
├── infrastructure       // Terraform scripts for creation of the Azure website
│
├── src                        // all projects
│   ├── build              // build settings
│          ├── dotnet     // .NET build settings
│   ├── Directory.Build.props   // project settings for all .NET projects in sub folders
│   ├── [Project folders]
│
├── x-cross
│   ├── toc.yml              // Cross reference definition (optional)
│
├── .markdownlint.json // Markdownlinter settings
├── docfx.json               // DocFx configuration
├── index.md                 // Website landing page
├── toc.yml                    // Definition of the website header content links
├── web.config              // web.config to enable search in deployed website
```

この`DocLinkChecker`ツールを使用して、ドキュメント内のすべてのリンクと孤立した添付ファイルを検証します。これが、`.attachments`フォルダにすべての添付ファイルがある理由です。

QuickStartフォルダーから生成されたWebサイトでは、ドキュメントと参照の階層が左側の目次に結合されていることがわかります。これは、**x-cross\toc.yml** の定義と使用によって実現されます。階層を結合したくない場合は、環境からフォルダーとファイルを削除して、Webサイトを（再）生成します。

以下の内容には`.markdownlint.json`が含まれています。[```MD013```の設定](https://github.com/markdownlint/markdownlint/blob/master/docs/RULES.md#md013---line-length)は、最大行長をチェックしないようにfalseに設定されています。このファイルを好みに合わせて変更し、特定のテストを含めたり除外したりできます。

```json
{
    "MD013": false
}
```

**.pipelines** フォルダーと **infrastructure** フォルダーの内容は、レシピ「[DocFxドキュメントWebサイトをAzure Webサイトに自動的にデプロイする](deploy-docfx-azure-website.md)」で説明されています。

## ソースコードからのリファレンスドキュメント

DocFxは、コードからリファレンスドキュメントを生成できます。現時点では、C＃とTypescriptが最適にサポートされています。QuickStartフォルダーでは、C＃プロジェクトのみを使用しました。DocFxが高品質のリファレンスドキュメントを生成するには、高品質のトリプルスラッシュコメントが必要です。[トリプルスラッシュ（///）コードコメントのサポート](https://dotnet.github.io/docfx/spec/triple_slash_comments_spec.html)を参照してください。これを強制するには、[StyleCop](https://github.com/DotNetAnalyzers/StyleCopAnalyzers)の使用を強制することをお勧めします。これを簡単に始めるためのいくつかのステップがあります。

まず、**/src** フォルダー内の**Directory.Build.props**ファイルを**build/dotnet**フォルダー内のファイルと組み合わせて使用​​できます。これにより、サブフォルダー内のすべてのVisual StudioプロジェクトファイルにStyleCopを適用し、どのルールを使用または無視するかを構成します。もちろん、これをニーズに合わせて調整することもできます。詳細については、「[ビルドのカスタマイズ](https://docs.microsoft.com/en-us/visualstudio/msbuild/customize-your-build?view=vs-2019)」および「[ルールセットを使用してコード分析ルールをグループ化する](https://docs.microsoft.com/en-us/visualstudio/code-quality/using-rule-sets-to-group-code-analysis-rules?view=vs-2019)」を参照してください。

開発者がコンパイラエラーをスローしてトリプルスラッシュコメントを追加し、ドキュメントXMLファイルを生成するための適切な設定を確実に行うには、すべての.csprojファイルに **TreatWarningsAsErrors** および **GenerateDocumentationFile** 設定を追加します。これは、次のように最初の *PropertyGroup* 設定に追加できます。

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
      ...
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <TreatWarningsAsErrors>true</TreatWarningsAsErrors>
  </PropertyGroup>

    ...
</Project>
```

これで、C＃コードからドキュメントを生成する準備が整いました。DocFxでサポートされている言語とその構成方法の詳細については、「[多言語サポートの概要](https://dotnet.github.io/docfx/tutorial/universalreference/intro_multiple_langs_support.html?q=typescript)」を参照してください。

> **注：** *Directory.Build.props* の2つの設定を使用して PropertyGroup 定義を追加し、すべてのプロジェクトでその設定を使用することもできます。ただし、その場合は、テストプロジェクトにも継承されます。

## 1. DocFxとmarkdownlint-cliをインストールします

DocFx Webサイトのダウンロードセクションにアクセスして、最新バージョンの[DocFx](https://dotnet.github.io/docfx/)をダウンロードします。[markdownlint-cliのgithubページ](https://github.com/igorshubovych/markdownlint-cli)に移動して、ダウンロードとインストールのオプションを見つけます。

[Chocolatey](https://chocolatey.org/)などのツールを使用してインストールすることもできます。

```bash
choco install docfx
choco install markdownlint-cli
```

## 2. DocFxを構成します

DocFxの構成は`docfx.json`ファイルで行われます。このファイルをリポジトリのルートに保存します。

> **注：** docfx.jsonは階層内の別の場所に保存できますが、ファイルのパスをdocfxコマンドの引数として指定して、ファイルを見つける必要があります。

以下は、最初に適切な構成です。ここで、ドキュメントは **/docs** フォルダーにあり、ソースは **/src** フォルダーにあります。

```json
{
    "metadata": [
    {
          "src": [
          {
              "files": [ "src/**.csproj" ],
              "exclude": [ "_site/**", "**/bin/**", "**/obj/**", "**/[Tt]ests/**" ]
          }
          ],
          "dest": "reference",
          "disableGitFeatures": false
       }
    ],
    "build": {
        "content": [
            { "files": [ "reference/**" ] },
            {
                "files": [ "**.md", "**/toc.yml" ],
                "exclude": [ "_site/**", "**/bin/**", "**/obj/**", "**/[Tt]ests/**" ]
            }
        ],
        "resource": [
            { "files": ["docs/.attachments/**"] },
            { "files": ["web.config"] }
        ],
        "template": [ "templates/cse" ],
        "globalMetadata": {
            "_appTitle": "CSE Documentation",
            "_enableSearch": true
        },
        "markdownEngineName": "markdig",
        "dest": "_site",
        "xrefService": ["https://xref.docs.microsoft.com/query?uid={uid}"]
    }
}
```

## 3. いくつかの基本的なドキュメントを設定します

**/docs** フォルダーにある基本的なドキュメント構造から始めることをお勧めします。提供されているQuickStartフォルダーには、基本的なセットアップがあります。

```xaml
├── docs
│   ├── .attachments                     // All images and other attachments used by documents
│
│   ├── architecture-decisions
│           └── .order
│           └── decision-log.md       // Sample index into all ADRs
│           └── README.md          // Landing page architecture decisions
│
│   ├── getting-started
│           └── .order
│           └── README.md          // This recipe document. Replace the content with something meaningful to the project
│
│   ├── guidelines
│           └── .order
│           └── docs-guidelines.md  // General documentation guidelines
│           └── README.md          // Landing page guidelines
│
│   ├── templates                          // all templates like ADR template and such
│           └── .order
│           └── README.md          // Landing page templates
│
│   ├── working-agreements
│           └── .order
│           └── README.md          // Landing page working agreements
│
│   ├── .order                                // Providing a fixed order of files and directories
│   ├── index.md                          // Landing page documentation
```

[CSE Playbook](https://github.com/microsoft/code-with-engineering-playbook/)の作業契約などのテンプレートを使用できます。

ドキュメントWebサイトの適切なランディングページを作成するには、リポジトリのルートにあるINDEX.MDというマークダウンファイルを使用できます。内容は次のようになります。

```markdown
# CSE Documentation

This is the landing page of the CSE Documentation website. This is the page to introduce everything on the website.

You can add specific links that are important to provide direct access.

> Try not to duplicate the links on the top of the page, unless it really makes sense.

To get started with the setup of this website, read the getting started document with the title [Using DocFx and Companion Tools](docs/getting-started/readme.md).

```

## 4. コンパニオンツールをコンパイルして実行します

> **注：** 各ステップを説明するために、次のいくつかの段落でさまざまなステップを実行します。提供されているサンプルには、 **GenerateDocWebsite.cmd** というバッチファイルが含まれています。このスクリプトは、ツールのコンパイル、チェックの実行、目次の生成、およびdocfxの実行に必要なすべての手順を実行してWebサイトを生成します。

適切なマークダウンフォーマットをチェックするには、**markdownlint-cli** ツールを使用します。このコマンドは `.markdownlint.json` 、プロジェクトのルートにあるファイルから構成を取得します。すべてのマークダウンファイルをチェックするには、次のコマンドを実行するだけです。

```shell
markdownlint **/*.md
```

この記事の概要で説明されているように、QuickStartフォルダーで、2つのコンパニオンツールである **TocDocFxCreation** と**DocLinkChecker** をコピーしておく必要があります。

Visual Studioからツールをコンパイルできますが、どちらのツールフォルダーでも`dotnet build`を実行することもできます。

**DocLinkChecker** コンパニオンツールは、docsフォルダーにあるものを検証するために使用されます。ドキュメントとドキュメントフォルダ内の添付ファイル間のリンクを検証し、孤立した添付ファイルがないかどうかを確認します。添付ファイルのチェックが含まれているこのツールの実行例：

```shell
DocLinkChecker.exe -d ./docs -a
```

**TocDocFxCreation** ツールは、ドキュメントの目次を生成するために必要です。これにより、ユーザーはフォルダーとドキュメント間を移動できます。ツールをコンパイルした場合は、このコマンドを使用して目次ファイル`toc.yml`を生成します。記事の順序を決定するために.orderファイルを使用して目次を生成し、フォルダーにデフォルトのドキュメントがない場合にindex.mdドキュメントを自動的に生成するには、次のコマンドを使用できます。

```shell
TocDocFxCreation.exe -d ./docs -sri
```

## 5. DocFxを実行してWebサイトを生成します

`docfx` コマンドを実行して、デフォルトで **_siteフォルダー** にWebサイトを生成します。

> **ヒント：** ローカル環境でWebサイトを確認する場合は、*docfx* コマンドまたは *GenerateDocWebsite* スクリプトのいずれかに **--serve** オプションを指定します。ローカルホストでアクセス可能なWebサイトをホストする小さなWebサーバーが起動します。

### ウェブサイトのスタイル

QuickStartフォルダーから始めた場合、Webサイトは、[マテリアルデザイン](https://ovasquez.github.io/docfx-material/)とMicrosoftロゴを使用したカスタムテーマを使用して生成されます。これはお好みに合わせて変更できます。詳細については、「[ハウツー：カスタムテンプレートの作成|DocFX Webサイト（dotnet.github.io）](https://dotnet.github.io/docfx/tutorial/howto_create_custom_template.html)」を参照してください。

## Azure Webサイトにデプロイする

手順を完了すると、*_site* フォルダーにデフォルトのWebサイトが生成されます。しかしもちろん、誰もがこれにアクセスできるようにする必要があります。したがって、次のステップは、たとえばAzure Webサイトを作成し、コンテンツを自動的に生成してそのWebサイトにデプロイするプロセスを用意することです。このプロセスは、「[DocFxドキュメントWebサイトをAzure Webサイトに自動的にデプロイする](deploy-docfx-azure-website.md)」レシピで説明されています。

## 参考文献

* [DocFX - 静的ドキュメントジェネレータ](https://dotnet.github.io/docfx/index.html)
* [DocFxドキュメントWebサイトをAzureWebサイトに自動的にデプロイする](deploy-docfx-azure-website.md)
* [DocFxとコンパニオンツールを使用してプロジェクトに高品質のドキュメントを提供する](https://mtirion.medium.com/providing-quality-documentation-in-your-project-with-docfx-and-companion-tools-76aed42b1ddd)
* [初心者のためのモノレポ](https://mtirion.medium.com/monorepo-for-beginners-45d5059ab40e)
