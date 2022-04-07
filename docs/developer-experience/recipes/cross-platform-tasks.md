# クロスプラットフォームタスク

クロスプラットフォームの互換性の問題を軽減するためのいくつかのオプションがあります。

- コンテナでのタスクの実行
- オペレーティングシステムに固有のコマンドを実行できるようにするオプションを提供するVSCodeのtasks-systemを使用します。

## Dockerまたはコンテナベース

コンテナーを開発マシンとして使用すると、開発者は最小限のセットアップで開始でき、コンテナーで実行することにより、ホストOSから開発環境を抽象化できます。DevContainersは、チーム全体でローカル開発者のエクスペリエンスを標準化するのにも役立ちます。

以下は、DevContainersでのタスクの実行を開始するための優れたリソースです。

- [コンテナ内での開発](https://code.visualstudio.com/docs/remote/containers)。
- [コンテナでの開発に関するチュートリアル](https://code.visualstudio.com/docs/remote/containers-tutorial)
- サンプルプロジェクトと開発コンテナテンプレートについては、[VS Code開発コンテナ レシピ](https://github.com/microsoft/vscode-dev-containers)を参照してください。
- [開発コンテナライブラリ](../devcontainers.md)

## VSCodeのタスク

### Node.jsを起動する

以下の例は、tasks.jsonを使用してコマンドとしてNode.js実行可能ファイルを実行する方法と、WindowsとLinuxで異なる方法で処理する方法についての洞察を提供します。

```json
{
  "label": "Run Node",
  "type": "process",
  "windows": {
    "command": "C:\\Program Files\\nodejs\\node.exe"
  },
  "linux": {
    "command": "/usr/bin/node"
  }
}
```

この例では、Node.jsを実行するために、特定のWindowsコマンドと特定のLinuxコマンドがあります。これにより、プラットフォーム固有のプロパティが可能になります。これらが定義されている場合、コマンドがWindowsオペレーティングシステムまたはLinuxで実行されるときに、デフォルトのプロパティの代わりに使用されます。

### カスタムタスク

すべてのスクリプトまたはタスクをワークスペースで自動検出できるわけではありません。独自のカスタムタスクを定義する必要がある場合があります。この例では、いくつかの環境を正しくセットアップするために実行するスクリプトがあります。スクリプトはワークスペース内のフォルダーに保存され、LinuxおよびmacOSの場合はtest.sh、Windowsの場合はtest.cmdという名前が付けられます。tasks.jsonファイルを使用すると、さまざまなオペレーティングシステムで何を実行するかを定義するカスタムタスクを使用して、このスクリプトの実行を可能にすることができます。

```json
{
  "version": "2.0.0",
  "tasks": [
    {
      "label": "Run tests",
      "type": "shell",
      "command": "./scripts/test.sh",
      "windows": {
        "command": ".\\scripts\\test.cmd"
      },
      "group": "test",
      "presentation": {
        "reveal": "always",
        "panel": "new"
      }
    }
  ]
}

```

ここでのコマンドはシェルコマンドであり、test.shまたはtest.cmdのいずれかを実行するようにシステムに指示します。デフォルトでは、指定されたパスでtest.shを実行します。この例では、Windows固有のプロパティも定義し、デフォルトの代わりにtest.cmdを実行するように指示しています。

### 参考文献

VS Code ドキュメント - [オペレーティングシステム固有のプロパティ](https://vscode-docs.readthedocs.io/en/stable/editor/tasks/#operating-system-specific-properties)
