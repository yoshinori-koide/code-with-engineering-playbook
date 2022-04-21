# YELPシークレット検知を使用したAzure DevOpsパイプラインでのシークレットの検出

## 概要

この記事では、YELPdetect-secretsをAzureDevOpsパイプラインに統合する方法に関する情報を見つけることができます。提案されたコードは、従来のCIプロセスの一部にすることも、（推奨される方法で）`main`ブランチにマージする前にPRの検証を構築することもできます。

## Azure DevOps パイプライン

提案されたAzureDevOpsパイプラインには、以下に説明する複数のステップが含まれています。

1. Python 3 をデフォルトとして設定
1. pipを使用して detect-secrets をインストールする
1. detect-secrets ツールを実行する
1. パイプラインアーティファクトで結果を公開する
   > 注：これはオプションの手順ですが、将来の調査では、結果を含む.jsonファイルが役立つ場合があります。
1. detect-secrets の結果の分析
   > 注：この手順では、.jsonファイルの簡単な分析を行います。シークレットが検出された場合は、終了コード1でビルドを中断します。

> 注：以下の例には、LinuxエージェントとWindowsエージェントの2つのジョブがあります。両方のジョブを使用する必要はありません。必要に応じてパイプラインを調整するだけです。
>
> 注：Windowsの例では、最新バージョンのdetect-secretsを使用していません。これは、detect-secretツールのバグに関連しています（詳細については、[Issue＃452](https://github.com/Yelp/detect-secrets/issues/452)を参照してください）。問題の修正を監視し、可能であればpip install コマンドでversion tag `==1.0.3`を削除して、最新バージョンを使用することを強くお勧めします。

```yaml
trigger:
  - none

jobs:
  - job: ubuntu
    displayName: "detect-secrets on Ubuntu Linux agent"
    pool:
      vmImage: ubuntu-latest
    steps:
      - task: UsePythonVersion@0
        displayName: "Set Python 3 as default"
        inputs:
          versionSpec: "3"
          addToPath: true
          architecture: "x64"

      - bash: pip install detect-secrets
        displayName: "Install detect-secrets using pip"

      - bash: |
          detect-secrets --version
          detect-secrets scan --all-files --force-use-all-plugins --exclude-files FETCH_HEAD > $(Pipeline.Workspace)/detect-secrets.json
        displayName: "Run detect-secrets tool"

      - task: PublishPipelineArtifact@1
        displayName: "Publish results in the Pipeline Artifact"
        inputs:
          targetPath: "$(Pipeline.Workspace)/detect-secrets.json"
          artifact: "detect-secrets-ubuntu"
          publishLocation: "pipeline"

      - bash: |
          dsjson=$(cat $(Pipeline.Workspace)/detect-secrets.json)
          echo "${dsjson}"

          count=$(echo "${dsjson}" | jq -c -r '.results | length')

          if [ $count -gt 0 ]; then
            msg="Secrets were detected in code. ${count} file(s) affected."
            echo "##vso[task.logissue type=error]${msg}"
            echo "##vso[task.complete result=Failed;]${msg}."
          else
            echo "##vso[task.complete result=Succeeded;]No secrets detected."
          fi
        displayName: "Analyzing detect-secrets results"

  - job: windows
    displayName: "detect-secrets on Windows agent"
    pool:
      vmImage: windows-latest
    steps:
      - task: UsePythonVersion@0
        displayName: "Set Python 3 as default"
        inputs:
          versionSpec: "3"
          addToPath: true
          architecture: "x64"

      - script: pip install detect-secrets==1.0.3
        displayName: "Install detect-secrets using pip"

      - script: |
          detect-secrets --version
          detect-secrets scan --all-files --force-use-all-plugins > $(Pipeline.Workspace)/detect-secrets.json
        displayName: "Run detect-secrets tool"

      - task: PublishPipelineArtifact@1
        displayName: "Publish results in the Pipeline Artifact"
        inputs:
          targetPath: "$(Pipeline.Workspace)/detect-secrets.json"
          artifact: "detect-secrets-windows"
          publishLocation: "pipeline"

      - pwsh: |
          $dsjson = Get-Content $(Pipeline.Workspace)/detect-secrets.json
          Write-Output $dsjson

          $dsObj = $dsjson | ConvertFrom-Json
          $count = ($dsObj.results | Get-Member -MemberType NoteProperty).Count

          if ($count -gt 0) {
            $msg = "Secrets were detected in code. $count file(s) affected. "
            Write-Host "##vso[task.logissue type=error]$msg"
            Write-Host "##vso[task.complete result=Failed;]$msg"
          }
          else {
            Write-Host "##vso[task.complete result=Succeeded;]No secrets detected."
          }
        displayName: "Analyzing detect-secrets results"
```
