# 開発コンテナ入門

開発者であり、Visual Studio Code（VS Code）またはDockerの経験がある場合は、おそらく[開発コンテナ](https://code.visualstudio.com/docs/remote/containers)（dev containers）を検討する必要があります。このreadmeは、開発者が開発コンテナを構築するために必要な意思決定プロセスを支援することを目的としています。提供されるガイダンスは、VSCodedevコンテナーを初めて使用する場合に特に役立ちます。

> **注：** このガイドは、CI/CD用に実行中のPythonプログラムをデプロイするためのDockerファイルの設定に関するものではありません。

## 前提条件

- VSCodeの経験
- Dockerの経験

## 開発コンテナとは何ですか？

開発コンテナーはVSCodeの機能であり、開発者はローカル開発ツールスタックをDockerコンテナーの内部にパッケージ化すると同時に、VSCodeUIエクスペリエンスを提供できます。Dockerコンテナ内にブレークポイントを設定したことがありますか？そうでないかもしれない。開発コンテナはそれを可能にします。これはすべて、Dockerと連携してDockerコンテナ内でVSCode Serverを起動する[Remote Development Extension Pack](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.vscode-remote-extensionpack)と呼ばれるVSCode拡張機能によって可能になります。VS Code UIコンポーネントはローカルのままですが、作業ファイルはコンテナーにボリュームマウントされます。以下の図は、[公式のVS Codeドキュメント](https://code.visualstudio.com/docs/remote/containers)から直接引用したもので、以下にこれを示します。

![image](https://user-images.githubusercontent.com/10041279/93239062-e1b9a480-f747-11ea-94fb-3d50b14fd9b1.png)

上記の図が明確でない場合、開発コンテナを直感的に理解するのに役立つ基本的な例えは、Dockerのインタラクティブモード（`docker exec -it 987654e0ff32`）と、慣れ親しんだVSCodeUIエクスペリエンスとの結合と考えることです。

上記の開発コンテナエクスペリエンスを設定するには、VSCodeのExtension Marketplaceを使用して[Remote Development Extension Pack](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.vscode-remote-extensionpack)をインストールします。

## 開発コンテナはどのようにプロジェクトのコラボレーションを改善できますか？

VS Code開発コンテナーは、2つの非常に具体的な問題に対処することにより、最近のチームプロジェクトでの開発者間のプロジェクトコラボレーションを改善しました。

- チーム内での一貫性のないローカル開発者の経験。
- プロジェクトに参加する開発者のオンボーディングが遅い。

上記の問題は、開発コンテナー定義を構成して共有することで解決されました。開発コンテナは、ベースイメージと、そのベースイメージをサポートするアーティファクトによって定義されます。ベースイメージとそれに付随するアーティファクトは、.devcontainerディレクトリにあります。このディレクトリは、構成が開始される場所です。開発コンテナ定義の中心的な成果物は、`devcontainer.json`と呼ばれる構成ファイルです。このファイルは、ベースイメージと開発コンテナのライフサイクルをサポートするために必要なアーティファクトを調整します。プロジェクトリポジトリ内でこのオーケストレーションを有効にするには、 [Remote Development Extension Pack](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.vscode-remote-extensionpack)をインストールする必要があります。

チームのすべての開発者は、コンテナーをスピンアップするために、devコンテナー定義（.devcontainerディレクトリー）を共有して使用することが期待されています。この定義は、チーム全体でアプリケーションをローカルで開発するための一貫したツールを提供します

以下のコードスニペットは、プロジェクトリポジトリ内の.devcontainerディレクトリとdevcontainer.jsonファイルの一般的な場所を示しています。また、Dockerファイルを参照する正しい方法も強調しています。

```bash
$ tree vs-code-remote-try-python  # main repo directory
└───.devcontainers
        ├───Dockerfile
        ├───devcontainer.json
```

```json
# devcontainer.json
{
    "name": "Python 3",
    "build": {
        "dockerfile": "Dockerfile",
        "context": "..",
        // Update 'VARIANT' to pick a Python version: 3, 3.6, 3.7, 3.8
        "args": {"VARIANT": "3.8"}
    },
}
```

devcontainer.json構成プロパティのリストについては、[開発コンテナのプロパティ](https://code.visualstudio.com/docs/remote/devcontainerjson-reference)に関するVSCodeのドキュメントを参照してください。

## どの開発コンテナが私のユースケースに適しているかをどのように判断しますか？

幸い、VS Codeには、開発コンテナー定義（.devcontainerディレクトリー）をホストするプラットフォーム固有のフォルダーのリポジトリギャラリーがあり、開発コンテナーの使用を簡単に開始できます。以下のコードスニペットは、[VSCode devコンテナーギャラリーリポジトリ](https://github.com/microsoft/vscode-dev-containers/tree/master/containers)から直接取得されたギャラリーフォルダーのリストを示しています。

```bash
$ tree vs-code-dev-containers  # main repo directory
└───containers
        ├───dotnetcore
        |   └───.devcontainers # dev container
        ├───python-3
        |   └───.devcontainers # dev container
        ├───ubuntu
        |   └───.devcontainers # dev container
        └───....
```

開発コンテナを構築するために必要な最後の高レベルの手順は次のとおりです。

1. ローカル開発ツールスタックを構築するプラットフォームを決定します。
2. プラットフォームを対象とするプロジェクトフォルダーのVSCodeが提供する開発コンテナーギャラリーを参照し、最も適切なものを選択します。
3. プロジェクトの開発コンテナ定義（.devcontainerディレクトリ）でベースイメージと、そのベースイメージをサポートするアーティファクトを調べます。
4. 発見したものを使用して、開発コンテナーをそのままセットアップしたり、拡張したり、独自のコンテナーを最初から構築したりします。

## もっと遠く行く

DevContainerの構成をさらに進めたい場合があります。[詳細はこちら](./going-further.md)
