# 開発コンテナをさらに活用する

開発コンテナを使用すると、開発者は共通の作業環境を共有でき、ランタイムとすべての依存関係のバージョンがすべての開発者に対して一貫していることが保証されます。

開発コンテナでは、次のことも可能です。

1. 既存のツールを活用して、より多くの機能を備えた開発コンテナを強化します。
2. 他の開発者にカスタムツール（スクリプトなど）を提供します。

## 既存のツール

開発フェーズでは、おそらく、開発コンテナにデフォルトでインストールされていないツールを使用する必要があります。たとえば、プロジェクトのターゲットをAzureにデプロイする場合は、リソースとアプリケーションのデプロイに[Azure-cli](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)と[Terraform](https://www.terraform.io/)が必要になります。このような開発コンテナは、[VSCode devコンテナギャラリーリポジトリ](https://github.com/microsoft/vscode-dev-containers/tree/master/containers)にあります。

他のいくつかのツールは次のとおりです。

* [Markdown](https://github.com/DavidAnson/markdownlint)ファイルのリンター、
* [bash](https://www.shellcheck.net/)スクリプトのリンター、
* など...

*ソースコードではない*ファイルをリンティングすることで、各開発者に共通のルールを持つ共通のフォーマットを確保できます。これらのチェックは[継続的インテグレーションパイプライン](https://docs.microsoft.com/en-us/azure/architecture/example-scenario/apps/devops-dotnet-webapp)でも実行する必要がありますが、[プルリクエスト](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/about-pull-requests)を開く前に実行することをお勧めします。

## カスタムツールの制限

開発コンテナーに[Azure-cli](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)を含めることにした場合、開発者はテナントに対してコマンドを実行できます。ただし、開発者の生活を楽にするために、`tenant ID`や`subscription ID`などの接続情報を安全かつ永続的な方法で事前入力できるようにすることで、さらに先に進むことができます（ [Docker](https://www.docker.com/)コンテナであるDevコンテナが削除される可能性があることを忘れないでください）、またはイメージが再構築される可能性があるため、*内部*のすべてのカスタマイズが失われます）。

これを実現する1つの方法は、環境変数を活用して、ソリューションの追跡されていない`.env`ファイル部分をDevContainerに注入することです。

次のファイル構造を検討してください。

```bash
My Application  # main repo directory
└───.devcontainer
|       ├───Dockerfile
|       ├───devcontainer.json
└───config
|       ├───.env
|       ├───.env-sample
```

このファイル`config/.env-sample`は追跡されたファイルであり、誰でも設定する環境変数を見つけることができます（明らかに値はありません）。

```bash
TENANT_ID=
SUBSCRIPTION_ID=
```

次に、リポジトリのクローンを作成する各開発者は、`config/.env`ファイルを作成し、適切な値を入力できます。

`.env`ファイルをコンテナに挿入するために、次のように`devcontainer.json`ファイルを更新できます。

```json
{
    ...
    "runArgs": ["--env-file","config/.env"],
    ...
}
```

Dev Containerが開始されるとすぐに、これらの環境変数がコンテナーに送信されます。

もう1つのアプローチは、Docker Composeを使用することです。これは、もう少し複雑で、おそらく環境変数だけでは**多すぎ**ます。Docker Composeを使用すると、カスタムDNS、ポート転送、複数のコンテナーなどの他の設定のロックを解除できます。

これを実現するには、次の`.devcontainer/docker-compose.yml`ファイルを追加する必要があります。

```yaml
version: '3'
services:
  my-workspace:
    env_file: ../config/.env
    build:
      context: .
      dockerfile: Dockerfile
    command: sleep infinity
```

`Dockerfile`の代わりに`docker-compose.yml`ファイルを使用するために、次のように`devcontainer.json`を調整する必要があります。

```json
{
    "name": "My Application",
    "dockerComposeFile": ["docker-compose.yml"],
    "service": "my-workspace"
    ...
}
```

このアプローチは、必要なものを準備することにより、他の多くのツールに適用できます。アイデアは、開発者の生活とプロジェクトに参加する新しい開発者を簡素化することです。

## カスタムツール

プロジェクトに取り組んでいる間、開発者はタスクを自動化するためのスクリプトを書くことになりかねません。このスクリプトは`bash`、`python`、または使い慣れたスクリプト言語であれば何でもかまいません。

書き込まれたすべてのマークダウンファイルが、設定した特定のルールに対して検証されていることを確認するとします。上で見たように、[markdownlint](https://github.com/DavidAnson/markdownlint)ツールを開発コンテナに含めることができます。ツールがインストールされているからといって、開発者がその使用方法を知っているわけではありません。

次のソリューション構造を検討してください。

```bash
My Application  # main repo directory
└───.devcontainer
|       ├───Dockerfile
|       ├───docker-compose.yml
|       ├───devcontainer.json
└───scripts
|       ├───check-markdown.sh
└───.markdownlint.json
```

`.devcontainer/Dockerfile`ファイルは[markdownlint](https://github.com/DavidAnson/markdownlint)をインストールします

```dockerfile
...
RUN apt-get update \
    && export DEBIAN_FRONTEND=noninteractive \
    && apt-get install -y nodejs npm

# Add NodeJS tools
RUN npm install -g markdownlint-cli
...
```

このファイル`.markdownlint.json`には、マークダウンファイルで検証するルールが含まれています（詳細については、[markdownlintサイト](https://github.com/DavidAnson/markdownlint)を参照してください）。

そして最後に、スクリプト`scripts/check-markdown.sh`には`markdownlint`を実行する次のコードが含まれています。

```bash
# Get the repository root
repoRoot="$( cd "$( dirname "${BASH_SOURCE[0]}" )/.." >/dev/null 2>&1 && pwd )"

# Execute markdownlint for the entire solution
markdownlint -c "${repoRoot}"/.markdownlint.json
```

開発コンテナが読み込まれると、開発者はターミナルで次のスクリプトを実行できるようになります。

```bash
/> ./scripts/check-markdown.sh
```

これは小さなユースケースであり、時間を節約するために開発者が行った作業を利用する他の無限の可能性があります。

## その他の考慮事項

### プラットフォームアーキテクチャ

ツールをインストールするときは、開発者が使用しているホストコンピューターを確実に把握する必要もあります。Windows、Linux、MacOのいずれを実行していても、すべてのIntelベースのコンピューターは同じ動作をします。ただし、最新のMacアーキテクチャ（Apple M1 / Silicon）はARM64であるため、開発コンテナを構築するときの動作は同じではありません。

たとえば、開発コンテナーに[Azure-cli](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)をインストールする場合、Intelベースのマシンの場合と同じようにインストールすることはできません。Intelベースのコンピューターでは、debパッケージをインストールできます。ただし、このパッケージはARMアーキテクチャでは使用できません。Linux ARMに[Azure-cli](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)をインストールする唯一の方法は、Pythonインストーラーの`pip`を使用することです。

これを実現するには、Dockerfileで、または外部bashスクリプトを呼び出して、ユニバーサルバージョンを持たない残りのツールをインストールすることにより、Devコンテナを構築するホストのアーキテクチャを確認する必要があります。

Dockerfileから呼び出すスニペットは次のとおりです。

```bash
# If Intel based, then use the deb file
if [[ `dpkg --print-architecture` == "amd64" ]]; then
    sudo curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash;
else
# arm based, install pip (and gcc) then azure-cli
    sudo apt-get -y install gcc
    python3 -m pip install --upgrade pip
    python3 -m pip install azure-cli
fi
```

### Githubのクレデンシャルの再利用

開発コンテナ内で開発する場合は、ホストと開発コンテナの間でGithubクレデンシャルを共有することもできます。そうすることで、sshキーを前後にコピーすることを回避できます（sshを使用してリポジトリにアクセスしている場合）。

1つのアプローチは、ローカルの`~/.ssh`フォルダーを開発コンテナーにマウントすることです。`devcontainer.json`の`mounts`のオプションを使用するか、DockerComposeを使用できます

* `mounts`を使う場合:

```json
{
    ...
    "mounts": ["source=${localEnv:HOME}/.ssh,target=/home/vscode/.ssh,type=bind"],
    ...
}
```

ご覧のとおり、`${localEnv:HOME}`はホストの`home`フォルダーを返し、コンテナの`home`フォルダーにマップします。

* Docker Compose を使う場合:

```yaml
version: '3'
services:
  my-worspace:
    env_file: ../configs/.env
    build:
      context: .
      dockerfile: Dockerfile
      - "~/.ssh:/home/alex/.ssh"
    command: sleep infinity
```

上記のように、DockerComposeを使用するには`devcontainer.json`ファイルを編集する必要があることに注意してください。

これで、永続性を気にすることなく、ホストマシンと同じクレデンシャルを使用してGithubにアクセスできます。

### カスタマイズを許可する

最後に、開発者がカスタマイズできるように環境にある程度の柔軟性を持たせることも興味深いことです。

たとえば、環境にエイリアスを追加したい場合があります。ただし、コンテナが破壊される可能性があるため、開発コンテナ内の`~/.bashrc`ファイルを変更することは適切なアプローチではありません。永続性を設定する方法は多数ありますが、ここに1つのアプローチがあります。

次のソリューション構造を検討してください。

```bash
My Application  # main repo directory
└───.devcontainer
|       ├───Dockerfile
|       ├───docker-compose.yml
|       ├───devcontainer.json
└───me
|       ├───bashrc_extension
```

フォルダ`me`はリポジトリで追跡されないため、開発者は個人的なリソースを柔軟に追加できます。これらのリソースの1つは、カスタマイズを含む`.bashrc`に拡張することができます。
例えば：

```bash
# Sample alias
alias gaa="git add --all"
```

これで、Dockerイメージのビルド時に`Dockerfile`の変更をロードするように調整できます（もちろん、ファイルがない場合は何もしません）。

```dockerfile
...
RUN echo "[ -f PATH_TO_WORKSPACE/me/bashrc_extension ] && . PATH_TO_WORKSPACE/me/bashrc_extension" >> ~/.bashrc;
...
```
