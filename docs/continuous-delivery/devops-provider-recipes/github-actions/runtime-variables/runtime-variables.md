# GitHubアクションのランタイム変数

## 目的

GitHub Actionsは、CI / CDパイプラインの作成と実行、特にGitHubでホストされるオープンソースプロジェクトで人気のある選択肢ですが、他のCI/CD環境に見られる特定の生活の質の機能が欠けています。GitHub Actionsがまだ実装していない重要な機能の1つは、パイプライン自体をテストするために、**ランタイム変数をモックしてワークフローに挿入する機能**です。

これにより、Azure DevOpsの既存の機能と、GitHubActions内でまだリリースされていない機能との間のブリッジが提供されます。

## ターゲットオーディエンス

このガイドは、CI / CDに精通しており、CI/CDパイプラインのセキュリティへの影響を理解していることを前提としています。また、基本的なCI / CDパイプラインの作成と実行、アクション内のリポジトリのチェックアウト、バージョン管理でのマーケットプレイスアクションの使用など、GitHubアクションに関する基本的な知識も前提としています。

CI / CDエンジニアとして、パイプラインとワークフローに環境変数または環境フラグを挿入してテストし、GitHubアクションを使用してこれを実現していることを前提としています。

## 使用シナリオ

多くの統合またはエンドツーエンドのワークフローには、実行時にのみ使用可能な特定の環境変数が必要です。たとえば、ワークフローは次のことを実行している可能性があります。

![Workflow Diagram](images/workflow-diagram.png)

この状況では、リソースへの外部呼び出しを行わずにパイプラインをテストすることは非常に困難です。多くの場合、リソースへの外部呼び出しは、コストまたは時間がかかる可能性があり、内部ループの開発が大幅に遅くなります。

例として、Azure DevOpsは、手動トリガーでパイプライン変数を定義する方法を提供します。

![AzDo Example](images/AzDoExample.PNG)

GitHubActionsはまだそうしていません。

## 解決

これを回避するための最も簡単な解決策は、コミットメッセージまたはPR本文のいずれかに、および変数にランタイム変数を追加し、`grep`することです。GitHub Actionsは、`contains` 関数をネイティブに使用して`grep` 機能を提供します。これを具体的に使用します。

範囲内：

- これをスコープして、既知のキーと値を使用して単一の環境変数をパイプラインに注入します。

範囲外：

- このソリューションは、シェルスクリプトやその他の変数作成手段を使用して明らかに拡張可能ですが、このソリューションは基本的な概念の証明として役立ちます。このガイドでは、そのようなスクリプトは提供されていません。
- さらに、チームは、提供されている変数の追加セクションがあるPRテンプレートを使用してこのプロセスを形式化することを望む場合があります。ただし、これはこのガイドには含まれていません。

> セキュリティ警告：
> **これはシークレットを挿入するためのものではありません。** コミットメッセージとPR本文はサードパーティによって取得され、`git log`に保存され、悪意のある個人がさまざまなツールを使用して読み取る可能性があるためです。むしろ、これは、上記のように、単純な変数を注入する必要があるワークフローをテストするためのものです。
> **シークレットまたは機密情報を取得する必要がある場合**は、[Github Action for Azure Key Vault](https://github.com/marketplace/actions/azure-key-vault-get-secrets)またはその他の同様のシークレットストレージおよび取得サービスを使用してください。

## メッセージ変数のコミット

**指定されたキーと値**を使用して、使用するために単一の変数を環境に注入する方法。この例では、キーは`COMMIT_VAR`であり、値は`[commit var]`です。

前提条件：

- パイプライントリガーは、プッシュされたコミットでトリガーするように正しく設定されています（ここで`actions-test-branch`は選択のブランチとして使用します）

コードスニペット：

```yaml
on:
  push:
    branches:
      - actions-test-branch

jobs:
  Echo-On-Commit:
    runs-on: ubuntu-latest
    steps:
      - name: "Checkout Repository"
        uses: actions/checkout@v2

      - name: "Set flag from Commit"
        env:
          COMMIT_VAR: ${{ contains(github.event.head_commit.message, '[commit var]') }}
        run: |
          if ${COMMIT_VAR} == true; then
            echo "flag=true" >> $GITHUB_ENV
            echo "flag set to true"
          else
            echo "flag=false" >> $GITHUB_ENV
            echo "flag set to false"
          fi

      - name: "Use flag if true"
        if: env.flag
        run: echo "Flag is available and true"
```

[ここ](amples/commit-example.yaml)で.YAMLとして利用できます。

コードの説明：

コードの最初の部分は、作業ブランチにプッシュトリガーを設定し、リポジトリをチェックアウトすることです。そのため、これについては詳しく説明しません。


```yaml
- name: "Set flag from Commit"
  env:
    COMMIT_VAR: ${{ contains(github.event.head_commit.message, '[commit var]') }}
```

これは、GitHubActionsパイプラインの唯一のジョブ内の名前付きステップです。ここでは、ステップの環境変数を設定します。ステップが呼び出すすべてのコードまたはアクションで、環境変数を使用できるようになります。

`contains`は、すべてのワークフローでデフォルトで使用できるGitHubアクション関数です。`true`または`false`のブール値を返します。この状況では、`github.event.head_commit.message`を使用してアクセスされた最後のプッシュのコミットメッセージかどうかを確認します。`${{...}}`は、GitHubコンテキストを使用し、関数と`github.event`変数をコマンドで使用できるようにするために必要です。

```yaml
run: |
  if ${COMMIT_VAR} == true; then
    echo "flag=true" >> $GITHUB_ENV
    echo "flag set to true"
  else
    echo "flag=false" >> $GITHUB_ENV
    echo "flag set to false"
  fi
```

`run`コマンドは、`COMMIT_VAR`変数が`true`に設定されているかどうかを確認し、設定されている場合は、セカンダリフラグをtrueに設定して、この動作をエコーし​​ます。変数が`false`の場合も同じです。

これを行う具体的な理由は、すべてのステップで`COMMIT_VAR`を再利用する代わりに、flag変数を以降のステップで使用できるようにするためです。さらに、スニペットの次の部分のように、アクションの`if`ステップでフラグを使用できるようにします。

```yaml
- name: "Use flag if true"
  if: env.flag
  run: echo "Flag is available and true"
```

スニペットのこの部分では、同じジョブの次のステップで、前のステップで設定した`flag`を使用しています。これにより、ユーザーは次のことができます。

1. GitHubコンテキストに繰り返しアクセスする代わりに、フラグを再利用します
2. 1つだけではなく、複数の条件を使用してフラグを設定します。たとえば、別のステップでフラグを設定しtrueたりfalse、別の理由で設定したりすることもできます。
3. 複数の場所で変数を変更するのではなく、正確に1つの場所で変数を変更します

より短い代替案：

「コミットからフラグを設定」ステップは、コードをはるかに短くするために次のように簡略化できますが、必ずしも読みやすくなるとは限りません。

```yaml
- name: "Set flag from Commit"
  env:
    COMMIT_VAR: ${{ contains(github.event.head_commit.message, '[commit var]') }}
  run: |
    echo "flag=${COMMIT_VAR}" >> $GITHUB_ENV
    echo "set flag to ${COMMIT_VAR}"
```

使用法：

変数を含む

1. `master`ブランチにプッシュ:

   ```cmd
   > git add.
   > git commit -m "Running Github Actions Test [commit var]"
   > git push
   ```

2. これにより、ワークフローがトリガーされます（他のプッシュも同様です）。`[commit var]`はコミットメッセージにあるため、`${COMMIT_VAR}`のワークフロー変数は`true`に設定され、結果は次のようになります。

   ![Commit True Scenario](images/CommitTrue.PNG)

変数を含まない

1. `master`ブランチにプッシュ:

   ```cmd
   > git add.
   > git commit -m "Running Github Actions Test"
   > git push
   ```

2. これにより、ワークフローがトリガーされます（他のプッシュも同様です）。`[commit var]`はコミットメッセージに**含まれていない**ため、`${COMMIT_VAR}`のワークフロー変数は`false`に設定され、結果は次のようになります。

   ![Commit False Scenario](images/CommitFalse.PNG)

## PRボディ変数

PRが作成されると、PR本文を使用して変数を設定することもできます。これらの変数は、そのPRに由来するすべてのワークフロー実行で利用できるようにすることができます。これにより、コミットメッセージがより有益で雑然としなくなり、開発者の作業が軽減されます。

繰り返しますが、これは予想されるキーと値です。この場合、キーは`PR_VAR`であり、値は`[pr var]`です。

前提条件：

- パイプライントリガーは、特定のブランチへのプルリクエストでトリガーするように正しく設定されています。（ここでは、マスターを宛先ブランチとして使用します。）

コードスニペット：

```yaml
on:
  pull_request:
    branches:
      - master

jobs:
  Echo-On-PR:
    runs-on: ubuntu-latest
    steps:
      - name: "Checkout Repository"
        uses: actions/checkout@v2

      - name: "Set flag from PR"
        env:
          PR_VAR: ${{ contains(github.event.pull_request.body, '[pr var]') }}
        run: |
          if ${PR_VAR} == true; then
            echo "flag=true" >> $GITHUB_ENV
            echo "flag set to true"
          else
            echo "flag=false" >> $GITHUB_ENV
            echo "flag set to false"
          fi

      - name: "Use flag if true"
        if: env.flag
        run: echo "Flag is available and true"
```

[ここ](examples/pr-example.yaml)で.YAMLとして利用できます。

コードの説明：

YAMLファイルの最初の部分は、プルリクエストトリガーを設定するだけです。次のコードの大部分は同一であるため、違いについてのみ説明します。

```yaml
- name: "Set flag from PR"
  env:
    PR_VAR: ${{ contains(github.event.pull_request.body, '[pr var]') }}
```

このセクションでは、`PR_VAR`環境変数は、`[pr var]`文字列がPR本文にあるかどうかで`true`もしくは`false`が設定されます。

より短い代替案：

上記と同様に、コードをはるかに短くするために、YAMLステップを次のように簡略化できますが、必ずしも読みやすくなるとは限りません。

```yaml
- name: "Set flag from PR"
  env:
    PR_VAR: ${{ contains(github.event.pull_request.body, '[pr var]') }}
  run: |
  echo "flag=${PR_VAR}" >> $GITHUB_ENV
  echo "set flag to ${PR_VAR}"
```

使用法：

1. `master`にプルリクエストを作成し、本文のどこかに期待される変数を含めます。

   ![PR Example](images/PRExample.PNG)

2. GitHubアクションは自動的にトリガーされ、`[pr var]`がPR本文に存在するため、flagは以下に示すように、trueに設定します。

   ![PR True](images/PRTrue.PNG)

## 実世界のシナリオ

環境変数の制御が非常に役立つ現実世界のシナリオはたくさんあります。いくつかの概要を以下に示します。

### 高価な外線通話の回避

開発者Aは、統合パイプラインの作成とテストを行っています。統合パイプラインは、Azure Data FactoryやDatabricksなどの外部サービスを呼び出し、結果を待ってから、その結果をエコーする必要があります。ワークフローは次のようになります。

![Workflow A](images/DevAWorkflow.png)

ワークフローは本質的に時間とコストがかかります。これは、応答を待つ間、Databricksクラスターを維持する必要があるためです。この外部依存関係は、ワークフローの他の部分の記述とテストの期間中、基本的に応答をモックし、実際の応答が重要でないか、直接テストされていない状況で応答をモックすることによって取り除くことができます。

### 長いCIプロセスをスキップする

開発者Bは、CI/CDパイプラインの作成とテストを行っています。パイプラインには複数のCIステージがあり、それぞれが順番に実行されます。ワークフローは次のようになります。

![Workflow B](images/DevBWorkflow.png)

この場合、各CIステージは次のステージが開始する前に実行する必要があり、プロセスの途中でエラーが発生すると、パイプライン全体が失敗する可能性があります。これは、状況によってはパイプラインの意図された動作である可能性があります（CIプロセスが失敗した場合に、より複雑で、より長いビルドを実行したり、時間のかかるテストカバレッジスイートを実行したりしたくない場合もあります）。パイプライン自体をテストするときにコメントアウトまたは削除されます。

代わりに、追加の手順で、コミットメッセージまたはPR本文のいずれかで`[skip ci $N]`タグをチェックし、CIビルドの特定の段階をスキップできます。これにより、ステップをコメントアウト/削除するときに時々発生するように、最終パイプラインに変更がコミットされて壊れることがなくなります。さらに、他のステップをスキップすることで個々のステップを繰り返しテストするメカニズムが可能になり、パイプラインの開発が大幅に容易になります。
