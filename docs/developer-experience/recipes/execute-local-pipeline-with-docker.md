# ローカルパイプラインを実行する

## 概要

パイプラインアクティビティをローカルで実行する機能を持つことは、開発者の前向きな経験を促進する機会として認識されています。このドキュメントでは、ローカルCIエクスペリエンスをCIサーバーのリモートプロセスと可能な限り類似させることができるソリューションについて説明します。

提案された方法を使用すると、次のことが可能になります。

- Build
- Lint
- Unit テスト
- E2E テスト
- ソリューションの実行
- OSや環境への非依存。

## Docker Compose の導入

[Docker Compose](https://docs.docker.com/compose/)を使用すると、プッシュを構築したり、マルチコンテナーDockerアプリケーションを実行したりできます。

### 作業方法

1. 可能であればビルドステップを含め、アプリケーションをDocker化してください。
2. Dockerファイルにステップを追加して、単体テストを実行します。
3. リンティング用のステップをDockerファイルに追加します。
4. おそらく別のフォルダーに新しいdockerfileを作成します。これにより、クラスターに対してエンドツーエンドのテストが実行されます。デフォルトのエンドポイントが構成可能であることを確認します（これは、リモートCIサーバーで便利になり、必要に応じて、ライブ環境に対してテストできます）。
5. 実行するサービスを選択できるdocker-composeファイルを作成します。デフォルトでは、すべてのアプリケーションとテストが実行され、オプションのパラメーターで特定のサービスを実行できます。たとえば、テストのないアプリケーションのみを実行できます。

### 前提条件

1. [Docker](https://www.docker.com/products/docker-desktop)
2. オプション：サンプルアプリのクローンを作成する場合は、[dotnet core](https://dotnet.microsoft.com/download) をインストールする必要があります。

### 例を使って段階的に

このチュートリアルでは、[sample dotnet core api application](https://github.com/itye-msft/cse-engagement-template)を使用します。
サンプルアプリのDockerファイルは次のとおりです。

```sh
# https://hub.docker.com/_/microsoft-dotnet
FROM mcr.microsoft.com/dotnet/sdk:5.0 AS build
WORKDIR /app

# copy csproj and restore as distinct layers
COPY ./ ./
RUN dotnet restore

RUN dotnet test

# copy everything else and build app
COPY SampleApp/. ./
RUN dotnet publish -c release -o out --no-restore

# final stage/image
FROM mcr.microsoft.com/dotnet/aspnet:5.0
WORKDIR /app
COPY --from=build /app/out .
ENTRYPOINT ["dotnet", "SampleNetApi.dll"]
```

このスクリプトは、すべての依存関係を復元し、テストをビルドして実行します。`stylecop`を含むdotnetアプリには、リンティングの問題が発生した場合にビルドに失敗するものが含まれています。

次に、エンドツーエンドのテストを実行するためのdockerfileも作成します。通常、これは一連のスクリプト、または実行中のアプリケーションへの実際のHTTP呼び出しを実行する専用アプリのように見えます。簡単にするために、dockerfile自体は単純なcurlコマンドを実行します。

```sh
FROM alpine:3.7
RUN apk --no-cache add curl
ENTRYPOINT ["curl","0.0.0.0:8080/weatherforecast"]
```

これで、両方のdockerfileをdocker-composeスクリプトに結合する準備が整いました。

```sh
version: '3'
services:
  app:
    image: app:0.01
    build:
      context: .
    ports:
      - "8080:80"
  e2e:
    image: e2e:0.01
    build:
      context: ./E2E
```

docker-composeスクリプトは2つのdockerfileを起動し、以前にビルドされていない場合はそれらをビルドします。次のコマンドは、dockercomposeを実行します。

```sh
docker-compose up --build -d
```

画像が表示されたら、サービスに電話をかけることができます。e2eイメージは、一連のe2eテストを実行します。テストをスキップしたい場合は、次のように、サービスの名前を追加することで、composeに特定のサービスを実行するように指示できます。

```sh
docker-compose up --build -d app
```

これで、アプリケーションをビルドしてテストするローカルスクリプトができました。次のステップは、CIにdocker-composeスクリプトを実行させることです。

AzureDevOpsパイプラインで使用されるyamlファイルの例を次に示します。

```sh
trigger:
- master

pool:
  vmImage: 'ubuntu-latest'

variables:
  solution: '**/*.sln'
  buildPlatform: 'Any CPU'
  buildConfiguration: 'Release'

steps:
- task: DockerCompose@0
  displayName: Build, Test, E2E
  inputs:
    action: Run services
    dockerComposeFile: docker-compose.yml
- script: dotnet restore SampleApp
- script: dotnet build --configuration $(buildConfiguration) SampleApp
  displayName: 'dotnet build $(buildConfiguration)'
```

このスクリプトでは、最初のステップはdocker-composeです。これは、前のステップで作成したものと同じファイルを使用します。次のステップは、スクリプトを使用して同じことを行い、比較のためにここにあります。このステップの終わりまでに、CIはローカルで実行するのと同じビルドおよびテストコマンドを効果的に実行します。
