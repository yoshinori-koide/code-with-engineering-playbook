# より良いドキュメントのためのCIパイプライン

## 序章

ほとんどのプロジェクトはスパイクから始まり、開発者とアナリストが大量のドキュメントを作成します。

場合によっては、これらのドキュメントに標準がなく、各チームメンバーが好みに応じてドキュメントを作成します。それに加えて、レビュアーが文法の確認、タイプミスまたは非包括的言語の検索に費やす時間。

このパイプラインはそれに対処するのに役立ちます！

## パイプライン

パイプラインは次の `npm` モジュールを使用します。

- [markdownlint](https://github.com/DavidAnson/markdownlint): [ルール](https://github.com/DavidAnson/markdownlint#rules--aliases)を使用して標準化を追加
- [markdown-link-check](https://github.com/tcort/markdown-link-check): ドキュメント内のリンクを確認し、壊れたリンクを報告します
- [write-good](https://github.com/btford/write-good): 英語の散文のためのリンター

私たちはこのパイプラインをさまざまな取り組みで1年以上使用しており、常にお客様から素晴らしいフィードバックを受け取りました。

## どのように機能しますか

このパイプラインの使用を開始するには:

1. [このリポジトリ](https://github.com/squassina/doc-pipeline/tree/main/repo-root)からファイルをダウンロードします
1. このリポジトリからファイルをダウンロードします
    - 新しいものでない場合は、ファイルをコピーして必要な調整を行います:
        - `.azdo` がリポジトリの標準と一致することを確認します。
        - 処理の中ですでに持っているものを上書きしないように`package.json`を確認します。`.azdo`フォルダの名前を変更した場合も、`package.json`ファイルを更新してください。
1. Azure DevOps または GitHub でパイプラインを作成します

## 参考文献

[Code With Engineering Playbook のマークダウンコードレビュー](https://microsoft.github.io/code-with-engineering-playbook/code-reviews/recipes/markdown/#code-review-checklist)
