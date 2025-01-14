# ブランチに名前を付ける

既存のプロジェクトに貢献するときは、合意されたブランチの命名規則を探してそれに従ってください。オープンソースプロジェクトでは、この情報は通常、寄稿の指示にあり、多くの場合、`CONTRIBUTING.md`という名前のファイルにあります。

新しいプロジェクトの開始時に、チームはブランチの命名戦略を含むプロジェクトの規則に同意します。

ブランチの命名規則の例を次に示します。

```plaintext
<user alias>/[feature/bug/hotfix]/<work item ID>_<title>
```

これは次のように解釈できます。

```plaintext
dickinson/feature/271_add_more_cowbell
```

上記の例はまさにその例です。チームは、パーツを省略するか追加するかを選択できます。ブランチ規則の選択は、開発モデル（[トランクベースの開発](https://trunkbaseddevelopment.com/)など）、[バージョン管理](component-versioning.md)モデル、ソース管理の管理に使用されるツール、好みの問題などに依存する可能性があります。単純さとあいまいさの削減に焦点を当てます。優れたブランチ命名戦略により、チームはリポジトリ内の各ブランチの目的と所有権を理解できます。
