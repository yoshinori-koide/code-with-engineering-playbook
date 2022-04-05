# Bashコードレビュー

※ オリジナル: https://microsoft.github.io/code-with-engineering-playbook/code-reviews/recipes/bash/

## スタイルガイド

[CSE](../../CSE.md)開発者は、[GoogleのBashスタイルガイド](https://google.github.io/styleguide/shell.xml)に従います。

## コード分​​析/リンティング

[CSE](../../CSE.md) プロジェクトは、 [CIプロセス](../../continuous-integration/README.md)の一部として[shellcheck](https://github.com/koalaman/shellcheck) を使用してbashコードをチェックする必要があります。リンティングとは別に、[shfmt](https://github.com/mvdan/sh)を使用してシェルスクリプトを自動的にフォーマットできます。シェルスクリプトを自動的にフォーマットするために使用できるshell-formatのようなshfmtに基づくvscodeコード拡張はほとんどありません。

## プロジェクトの設定

### vscode-shellcheck

Shellcheck拡張機能はVSCodeで使用する必要があり、静的コード分析機能とリンティングの問題の自動修正を提供します。vscodeでvscode-shellcheckを使用するには、次のようにします。

#### マシンにshellcheckをインストールします

macOSの場合

```bash
brew install shellcheck
```

Ubuntuの場合：

```bash
apt-get install shellcheck
```

#### vscodeにshellcheckをインストールします

vscodeでvscode-shellcheck拡張機能を見つけてインストールします。

## 自動コードフォーマット

### シェル形式

shell-format拡張機能は、bashスクリプト、dockerファイル、およびいくつかの構成ファイルの自動フォーマットを行います。これは、bashのグーグルスタイルガイドチェックを実施できるshfmtに依存しています。
vscodeでshell-formatを使用するには、次のようにします。

#### マシンにshfmt（Go 1.13以降が必要）をインストールします

```bash
GO111MODULE=on go get mvdan.cc/sh/v3/cmd/shfmt
```

#### vscodeにshell-formatをインストールする

vscodeでシェル形式の拡張機能を見つけてインストールします。

## ビルド検証

Azure DevOpsでこのプロセスを自動化するには、次のスニペットを`azure-pipelines.yaml`ファイルに追加します。これにより、`./scripts/`フォルダ内のすべてのスクリプトが削除されます。

```yaml
- bash: |
    echo "This checks for formatting and common bash errors. See wiki for error details and ignore options: https://github.com/koalaman/shellcheck/wiki/SC1000"
    export scversion="stable"
    wget -qO- "https://github.com/koalaman/shellcheck/releases/download/${scversion?}/shellcheck-${scversion?}.linux.x86_64.tar.xz" | tar -xJv
    sudo mv "shellcheck-${scversion}/shellcheck" /usr/bin/
    rm -r "shellcheck-${scversion}"
    shellcheck ./scripts/*.sh
  displayName: "Validate Scripts: Shellcheck"
```

また、`shfmt`ツールを使用して、ビルドパイプラインでシェルスクリプトをフォーマットできます。`shfmt`ビルドパイプラインに統合するには、次の手順を実行します。

```yaml
- bash: |
    echo "This step does auto formatting of shell scripts"
    shfmt -l -w ./scripts/*.sh
  displayName: "Format Scripts: shfmt"
```

次のブロックを使用して、[shunit2](https://github.com/kward/shunit2)を使用した単体テストをビルドパイプラインに追加することもできます。

```yaml
- bash: |
    echo "This step unit tests shell scripts by using shunit2"
    ./shunit2
  displayName: "Format Scripts: shfmt"
```

## 事前コミットフック

すべての開発者は、事前コミットフックとしてshellcheckとshfmtを実行する必要があります。

### Step 1- pre-commitをインストールする

pre-commitをインストールするために`pip install pre-commit`を実行します。
または、homebrewを使用している場合は`brew install pre-commit`を実行できます。

### Step 2- shellcheckとshfmtを追加する

pre-commit-config.yamlファイルをgoプロジェクトのルートに追加します。以下のように.pre-commit-config.yamlファイルにshfmtを追加して、pre-commitでshfmtを実行します。
```yaml
-   repo: git://github.com/pecigonzalo/pre-commit-fmt
    sha: master
    hooks:
      -   id: shell-fmt
          args:
            - --indent=4
```

```yaml
-   repo: https://github.com/shellcheck-py/shellcheck-py
    rev: v0.7.1.1
    hooks:
    -   id: shellcheck
```

### Step 3

`$ pre-commit install`を実行してgitフックスクリプトを設定します

## 依存関係

Bashスクリプトは、他のシステムやツールを「接着」するためによく使用されます。そのため、Bashスクリプトには、多くの場合、多数の依存関係や複雑な依存関係があります。Dockerコンテナーを使用して、すべての正しい依存関係が含まれることが保証されている、移植可能で再現可能な環境でスクリプトが実行されるようにすることを検討してください。それでもDocker化されたスクリプトを簡単に実行できるようにするには、スクリプトがDockerで実行されているかどうかを確認し、実行されていない場合はDockerで再実行する「ブートストラップ」でスクリプトをラップすることにより、Dockerの使用をスクリプトの呼び出し元に対して透過的にすることを検討してください。ケース。これにより、スクリプトの実行が簡単で、環境が一貫しているという、両方の長所が得られます。

```bash
if [[ "${DOCKER}" != "true" ]]; then
  docker build -t my_script -f my_script.Dockerfile . > /dev/null
  docker run -e DOCKER=true my_script "$@"
  exit $?
fi

# ... implementation of my_script here can assume that all of its dependencies exist since it's always running in Docker ...
```

## コードレビューチェックリスト

[コードレビューチェックリスト](../process-guidance/reviewer-guidance.md)に加えて、これらのbash固有のコードレビュー項目も探す必要があります

* [ ] このコードは、シェルスクリプトの実行制御にset -o、set -e、set -uなどの[組み込みシェル](https://www.gnu.org/software/bash/manual/html_node/The-Set-Builtin.html)オプションを使用していますか？
* [ ] コードはモジュール化されていますか？シェルスクリプトは、Pythonモジュールのようにモジュール化できます。bashスクリプトの一部は、複雑なbashプロジェクトで調達する必要があります。
* [ ] すべての例外は正しく処理されていますか？例外は、終了コードまたはトラップ信号を使用して正しく処理する必要があります。
* [ ] コードは、shellcheckに従ってすべてのリンティングチェックに合格し、shunit2に従ってユニットテストに合格しますか？
* [ ] コードは相対パスまたは絶対パスを使用していますか？相対パスは環境攻撃を受けやすいため、避ける必要があります。相対パスが必要な場合は、```PATH```変数が設定されていることを確認してください。コードは相対パスまたは絶対パスを使用していますか？相対パスは環境攻撃を受けやすいため、避ける必要があります。相対パスが必要な場合は、PATH変数が設定されていることを確認してください。
* [ ] コードはユーザー入力としてクレデンシャルを取りますか？スクリプトでクレデンシャルがマスクまたは暗号化されていますか？
