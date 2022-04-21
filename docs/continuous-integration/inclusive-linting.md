# 包括的リンティング

ソフトウェアの専門家として、私たちは包括的な作業環境を促進するよう努めるべきです。それは当然、私たちが作成するコードとドキュメントにまで及びます。プロジェクト全体またはリポジトリ全体で包括的言語の使用を一貫させることが重要です。

これを実現するには、包括的リンターなどのテキストファイル分析ツールを使用し、これをCIパイプラインのステップとして含めることをお勧めします。

## 何のためにリントするか

包括的リンターの主な目標は、ソースコード内での非包括的言語の出現にフラグを立てることです（オプションでいくつかの代替案を提案します）。プロジェクト内の包括的でない単語やフレーズは、コメントやドキュメントから変数名まで、どこにでもあります。

包括的リンターには、適切な開始点として実行するための「デフォルト」の非包括的単語およびフレーズの独自の辞書が含まれる場合があります。これらのツールはカスタマイズすることもでき、多くの場合、一部の用語を省略したり、独自の用語を追加したりすることができます。

リンターに用語を追加する機能には、包括的リンティングに加えて、機密性の高い言語のリンティングを可能にするという追加の利点があります。これにより、たとえば、顧客名やその他の非公開情報がgit履歴に反映されないようにすることができます。

## インクルーシブリンター入門

### [`woke`]

私たちがお勧めする包括的なリンターの1つは`woke`です。これは言語に依存しないCLIツールであり、ソースコード内の包括的でない言語を検出し、代替言語を推奨します。`woke`では非包括的用語を含むデフォルトのルールセットをlintforに自動的に適用しますが、追加の用語を含むカスタムルール構成を（yamlファイルを介して）lintforに適用することもできます。カスタムルールの追加例については、[`example.yaml`]を参照してください。

ファイルまたはディレクトリでツールをローカルで実行するのは比較的簡単です。

```sh
$ woke test.txt

test.txt:2:2-6: `guys` may be insensitive, use `folks`, `people` instead (warning)
* guys
  ^
```

`woke`はCLIを介してマシンまたはCI/CDシステムでローカルに実行でき、2つのGitHubアクションとしても利用できます。

- [Run woke]
- [Run woke with Reviewdog]

CIパイプラインのデフォルトのルールセットで標準の「Runwoke」GitHubアクションを使用するには：

1. プロジェクトのCIパイプラインyamlにステップとして`woke`アクションを追加します。

    ```yaml
    name: ci
    on:
      - pull_request
    jobs:
      woke:
        name: woke
        runs-on: ubuntu-latest
        steps:
          - name: Checkout
            uses: actions/checkout@v2

          - name: woke
            uses: get-woke/woke-action@v0
            with:
              # Cause the check to fail on any broke rules
              fail-on-error: true
    ```

1. パイプラインを実行する
1. メインリポジトリビューの[アクション]タブで出力を表示します

追加の構成と使用法の詳細については、[公式ドキュメント]を参照してください。

[`woke`]: https://github.com/get-woke/woke
[default ruleset]: https://github.com/get-woke/woke/blob/main/pkg/rule/default.yaml
[`example.yaml`]: https://github.com/get-woke/woke/blob/main/example.yaml
[Run woke]: https://github.com/marketplace/actions/run-woke
[Run woke with reviewdog]: https://github.com/marketplace/actions/run-woke-with-reviewdog
[公式ドキュメント]: https://docs.getwoke.tech/
