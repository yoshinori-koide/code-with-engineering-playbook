# JavaScript/TypeScriptコードレビュー

※ オリジナル: https://microsoft.github.io/code-with-engineering-playbook/code-reviews/recipes/javascript-and-typescript/

## スタイルガイド

[CSE](../../CSE.md)開発者は、JavaScriptのコードフォーマットを行うために[prettier](https://prettier.io/)を使用します。

Prettierのような自動化されたコードフォーマットツールを使用すると、Microsoft、Facebook、AirBnBなどの幅広い企業が共同で作成した広く受け入れられているスタイルガイドが適用されます。

prettierにカバーされていないより高いレベルのスタイルガイダンスについては、[AirBnBスタイルガイド](https://github.com/airbnb/javascript)に従ってください。

## コード分​​析/リンティング

### eslint

[Palantirの2019TSLintロードマップ](https://medium.com/palantir/tslint-in-2019-1a144c2317a9)で概説されているガイダンスに従って、TypeScriptコードはESLintでリントする必要があります。[ESLint](https://github.com/eslint/eslint)を使用したTypeScriptコードのリンティングの詳細については、[typescript-eslint](https://typescript-eslint.io/)のドキュメントを参照してください。

[ESLintを使用してリンティングをインストールおよび構成する](https://typescript-eslint.io/)には、次のパッケージをdev-dependenciesとしてインストールします。

```bash
npm install -D eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin
```

プロジェクトのルートに`.eslintrc.js`を追加します。

```javascript
module.exports = {
  root: true,
  parser: '@typescript-eslint/parser',
  plugins: [
    '@typescript-eslint',
  ],
  extends: [
    'eslint:recommended',
    'plugin:@typescript-eslint/eslint-recommended',
    'plugin:@typescript-eslint/recommended',
  ],
};
```

`package.json`の`scripts`に以下を追加してください：

```json
"scripts": {
    "lint": "eslint . --ext .js,.jsx,.ts,.tsx --ignore-path .gitignore"
}
```

これにより、プロジェクト内のすべての`.js`、`.jsx`、`.ts`、`.tsx`ファイルがリントされ、`.gitignore`で指定されたファイルまたはディレクトリが省略されます。

次のコマンドでリンティングを実行できます。

```bash
npm run lint
```

## Prettierのセットアップ

[Prettier](https://prettier.io/docs/en/)は、代表的なコードフォーマッターです。

[スタートガイド](https://prettier.io/docs/en/integrating-with-linters.html)。

dev-dependencyとして`npm`インストールします。

```bash
npm install -D prettier eslint-config-prettier eslint-plugin-prettier
```

`.eslintrc.js`に `prettier` を追記します。

```javascript
module.exports = {
  root: true,
  parser: '@typescript-eslint/parser',
  plugins: [
    '@typescript-eslint',
  ],
  extends: [
    'eslint:recommended',
    'plugin:@typescript-eslint/eslint-recommended',
    'plugin:@typescript-eslint/recommended',
    'prettier/@typescript-eslint',
    'plugin:prettier/recommended',
  ],
};
```

これにより、ESLintでリントするときに`prettier`ルールセットが適用されます。

## VSCodeによる自動フォーマット

VS Codeは、保存時に自動的に`eslint --fix`が実行されるように構成できます。

`.vscode`プロジェクトのルートにフォルダーを作成し、以下を`.vscode/settings.json`に追加します。

```json
{
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  },
}
```

デフォルトでは、VS Code構成に次のオーバーライドを追加して、一重引用符、4つのスペースの削除を標準化し、ESLintingを実行する必要があります。

```json
{
    "prettier.singleQuote": true,
    "prettier.eslintIntegration": true,
    "prettier.tabWidth": 4
}
```

## ビルド検証

Azure Devopsでこのプロセスを自動化するには、パイプライン定義のyamlファイルに次のスニペットを追加します。これにより、`./scripts`フォルダ内のすべてのスクリプトが削除され/ます。

```yaml
- task: Npm@1
  displayName: 'Lint'
  inputs:
    command: 'custom'
    customCommand: 'run lint'
    workingDir: './scripts/'
```

## フックの事前コミット

すべての開発者は`eslint`を、標準のフォーマットを確保するためにpre-commitフックで実行する必要があります。リアルタイムのフィードバックを提供するには、[vscode-eslint](https://github.com/Microsoft/vscode-eslint)などのエディター統合を使用することを強くお勧めします。

1. `.git/hooks`以下の`pre-commit.sample`を`pre-commit`に名前を変更します。
2. そのファイル内の既存のサンプルコードを削除します
3. [pre-commit-eslint](https://gist.github.com/linhmtran168/2286aeafe747e78f53bf)のように、要点についてはこのためのスクリプトの例がたくさんあります。
4. TypeScriptファイルを含めるように適宜変更します（ts拡張子を含め、typescript-eslintが設定されていることを確認してください）
5. ファイルを実行可能にします。: `chmod +x .git/hooks/pre-commit`

別の方法として、[husky](https://github.com/typicode/husky) は事前コミットフックを単純化することを検討できます。

## コードレビューチェックリスト

[コードレビューチェックリスト](../process-guidance/reviewer-guidance.md)に加えて、これらのJavaScriptおよびTypeScript固有のコードレビュー項目も探す必要があります。

### Javascript

* [ ] コードはフォーマットとコード標準に準拠していますか？コード上でprettierとESLintを実行しても、それぞれ警告やエラーは発生しませんか？
* [ ] エコシステムからよく知られたモジュールを取り込むことで、より適切に機能するコードを再実装する変更はありますか？
* [ ] 宣言されていない変数のエラーを減らすために`"use strict";`が使用されていますか？
* [ ] APIについても、可能な場合は単体テストを使用していますか？[Ponicode](https://www.ponicode.com/)は、テストの生成に役立ちます。Ponicodeは、Jest構文を使用してテストファイルを作成します。
* [ ] テストは**Arrange/Act/Assert**パターンで正しく配置され、このように適切に文書化されていますか？
* [ ] `try catch finally`ステートメントだけでなく、エラー処理のベストプラクティスに従っていますか？
* [ ] `expect`、`done`を含む、非同期呼び出しは適切に`doWork().then(doSomething).then(checkSomething)`で実行されていますか？
* [ ] 生の文字列を使用する代わりに、定数はメインクラスで使用されますか？または、これらの文字列がファイル/クラス全体で使用されている場合、定数の静的クラスはありますか？
* [ ] マジックナンバーは説明されていますか？少なくともなぜそこにあるのかについてのコメントがなければ、コードに数字があってはなりません。数が繰り返される場合、定数/列挙型または同等のものはありますか？
* [ ] 非同期メソッドがある場合、メソッドの名前は`Async`サフィックスで終わりますか？
* [ ] 最小レベルのロギングが実施されていますか？使用されるロギングレベルは賢明ですか？
* [ ] ドキュメントフラグメントの操作は、複数のサブ要素を操作する必要がある場合に限定されていますか？

### TypeScript

* [ ] コードはフォーマットとコード標準に準拠していますか？コード上でprettierとESLintを実行しても、それぞれ警告やエラーは発生しませんか？
* [ ] エコシステムからよく知られたモジュールを取り込むことで、より適切に機能するコードを再実装する変更はありますか？
* [ ] TypeScriptコードはリンティングエラーを発生させずにコンパイルされますか？
* [ ] 生の文字列を使用する代わりに、定数はメインクラスで使用されますか？または、これらの文字列がファイル/クラス全体で使用されている場合、定数の静的クラスはありますか？
* [ ] マジックナンバーは説明されていますか？少なくともなぜそこにあるのかについてのコメントがなければ、コードに数字があってはなりません。数が繰り返される場合、定数/列挙型または同等のものはありますか？
* [ ] さまざまなクラスやメソッドに適切な`/* */`コメントはありますか？
* [ ] 可能な場合、単体テストが使用されますか？ほとんどの場合、テストはAPI、データアクセスとのインターフェース、変換、バックエンド要素およびモデルに対して存在する必要があります。[Ponicode](https://www.ponicode.com/)は、テストの生成に役立ちます。
* [ ] テストは**Arrange/Act/Assert**パターンで正しく配置され、このように適切に文書化されていますか？A
* [ ] 非同期メソッドがある場合、メソッドの名前は`Async`サフィックスで終わりますか？
* [ ] 最小レベルのロギングが実施されていますか？ロギングレベルは正しいですか？
* [ ]ドキュメントフラグメントの操作は、複数のサブ要素を操作する必要がある場合に限定されていますか？
* [ ] 重い操作がバックエンドに実装され、コントローラーが可能な限り薄くなっていますか？
* [ ] HTMLでのイベント処理は効率的に行われますか？
