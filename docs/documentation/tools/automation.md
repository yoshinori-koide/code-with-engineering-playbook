# 簡単なチェックを自動化する方法

Markdownドキュメントのチェックを自動化したい場合は、活用できるツールがいくつかあります。例えば：

- [コード分​​析/リンティング](../../code-reviews/recipes/markdown.md#code-analysis-linting)
  - [markdownlint](../../code-reviews/recipes/markdown.md#markdownlint)は、Markdown構文を検証し、テキストを読みやすくするルールを適用します。
  - [markdown-link-check](https://github.com/tcort/markdown-link-check)は、マークダウンテキストからリンクを抽出し、各リンクが有効（200 OK）か無効かを確認します。
  - [proselint](../../code-reviews/recipes/markdown.md#proselint)は、専門用語、スペルミス、冗長性、企業の話し方、その他の言語関連の問題をチェックします。
  - [write-good](../../code-reviews/recipes/markdown.md#write-good)は、英語の文法をチェックします。
  - [node-markdown-spellcheck 用のDockerイメージ](https://github.com/tmaier/docker-markdown-spellcheck)はマークダウンファイルをスペルチェックするための軽量のDockerイメージです。

- [VS Code 拡張機能](../../code-reviews/recipes/markdown.md#vs-code-extensions)
  - [Write Good Linter](../../code-reviews/recipes/markdown.md#write-good-linter)はドキュメントの編集中に文法や言語のアドバイスを得られます。
  - [markdownlint](../../code-reviews/recipes/markdown.md#markdownlint-extension)は、Markdownドキュメントを調べ、編集中にルール違反の警告を受け取ります。

- オートメーション
  - [pre-commit](https://pre-commit.com/)はレビューのためにコードまたはドキュメントを送信する前に、Gitフックスクリプトを使用して簡単な問題を特定できます。
  - [Build validation](../../code-reviews/recipes/markdown.md#build-validation)をオンにしてPRのリンティングを自動化できます。
  - `markdownlint`、`markdown-link-check`および、`write-good`を使用するサンプルのパイプラインについては、「[より良いドキュメントのためのCIパイプライン](../../continuous-integration/markdown-linting/README.md)」を参照してください。 

サンプル出力：

![docs-checks](./images/docs-checks.png)

## リンティングルールについて

チームは、必要なリンティングルールを明確にする必要があり、ツールやコメントで上書きしないでください。チームは、ツールルールをいつオーバーライドするかについてコンセンサスを得る必要があります。
