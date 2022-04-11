# ソース管理

ソース管理を使用する場合、多くのオプションがあります。[CSE](../CSE.md)では、プライベートリポジトリに[AzureDevOps](https://azure.microsoft.com/en-us/services/devops/)を使用し、パブリックリポジトリに[GitHub](https://github.com/)を使用します。

## ソース管理内のセクション

* [マージ戦略](merge-strategies.md)
* [ブランチの命名](naming-branches.md)
* [バージョニング](component-versioning.md)
* [秘密の取り扱い](secrets-management.md)
* [Gitガイダンス](git-guidance/README.md)

## ゴール

* 業界のベストプラクティスに従って、[CSE](../CSE.md)全体およびより広範なOSSコミュニティからの貢献を奨励する地理的に分散したチームで作業する
* メインブランチにマージする前にレビューを実施することにより、コードの品質を向上させます
* クリーンなコミット履歴を通じて機能と修正のトレーサビリティを改善します

## 一般的なガイダンス

一貫性が重要であるため、コーディングを開始する前に、チームとしてのアプローチに同意してください。これを設計上の決定として扱い、すべての設計上の決定を文書化するのと同じ方法で、設計の提案とレビューを含めます（[作業契約](../agile-development/team-agreements/working-agreements.md)と[設計レビュー](../design/design-reviews/README.md)を参照）。

## 新しいリポジトリの作成

新しいリポジトリを作成する場合、チームは少なくとも次のことを行う必要があります

* **ブランチ**、 **リリース**、 **マージ戦略** について合意する
* マージ戦略を定義します（[線形または非線形](merge-strategies.md)）
* デフォルトのブランチをロックし、[プルリクエスト（PR）](../code-reviews/pull-requests.md)を使用してマージします
* [ブランチの命名](naming-branches.md)に同意する（例user/your_alias/feature_name）
* [ブランチ/PRポリシー](../code-reviews/pull-requests.md)を確立する
* パブリックリポジトリの場合、デフォルトのブランチには次のファイルが含まれている必要があります。
  * [LICENSE](../resources/templates/LICENSE)
  * [README.md](../resources/templates/README.md)
  * [CONTRIBUTING.md](../resources/templates/CONTRIBUTING.md)

## 既存のリポジトリへの貢献

既存のプロジェクトで作業する場合は、リポジトリを`git clone`し、チームのブランチ、マージ、およびリリース戦略を確実に理解してください（たとえば、プロジェクトの[CONTRIBUTING.mdファイル](https://blog.github.com/2012-09-17-contributing-guidelines/)を使用）。

## 混合DevOps環境

単一のホストされたDevOps環境（つまり、AzureDevOps）を持つほとんどのエンゲージメントでは、推奨されるパスですが、顧客の要件により、混合DevOps環境（つまり、アジャイル/作業項目の追跡用のAzureDevOpsとソース管理用のGitHub）が必要になる場合があります。混合環境で作業する場合：

* 作業項目のPRに手動でタグを付ける
* 作業項目/タスクの範囲がPRと一致していることを確認します

## ベストプラクティスをコミットする

* 小さなコミットを行います。これにより、変更の確認が容易になり、コミットを元に戻す必要がある場合に失う作業が少なくなります。
* 完全で十分にテストされたコードをコミットします。不完全なコードをコミットしないでください。コミットする前にコードをテストする習慣を身に付けてください。
* 空白の変更と関数型コードの変更を混在させないでください。行に機能的な変更があるのか​​、空白だけを削除するのかを判断するのは難しいため、機能的な変更が見過ごされる可能性があります。
* 良いコミットメッセージを書いてください。

良いコミットメッセージはこれらの質問に答えるはずです：

* なぜそれが必要なのですか？バグを修正したり、機能を追加したり、パフォーマンスを向上させたり、正確さのために変更したりする場合があります
* この問題にどのように対処しますか？短くて明らかな変更の場合、これは省略できます
* この変更にはどのような影響がありますか？明らかなものに加えて、これにはベンチマーク、副作用などが含まれる場合があります。
* 現在のコードにはどのような制限がありますか？

コミットメッセージを書くときは、次のことを考慮してください。

* コードが自明/自己文書化されていると思い込まないでください
* コミットを要約するのが難しいと思われる場合は、複数の論理的な変更またはバグ修正が含まれている可能性があります。もしそうなら、`git add -p`を使って別々のコミットに分割することをお勧めします
* レビュー担当者が元の問題を理解していると思い込まないでください。元のバグ/タスクの内容を読まなくても、変更要求を確認できるはずです。

良いメッセージ構造：

* 件名と本文を空白行で区切る
* 件名を50文字に制限します
* 件名を大文字にする
* 件名をピリオドで終わらせない
* 件名に命令法を使用します（*ログのタイプミスを修正する* か、*ログのタイプミスを修正する* か、*ログコードのその他の修正を行う*）
* 本文は72文字で改行する
* Use the body to explain what and why vs. how
* 本文では、何を、なぜ、どのよう、を説明する
* [キーワードを閉じる](https://help.github.com/en/enterprise/2.16/user/github/managing-your-work-on-github/closing-issues-using-keywords)際の修正された問題を参照する

適切に構造化されたgitcommitメッセージの例：

```md
Add code review recipe for Go

- Helps teams automate linting and build verification for go projects.
- Also gives a list of items to verify for go code reviews.

The PR does not add info about VS Code extensions for go, this will
be added in issue #124

Closes: #123
```

デフォルトのgitエディターを指定できます。これにより、お気に入りのエディターを使用してコミットメッセージを書き込むことができます。次のコマンドは、Visual Studio Codeをデフォルトのgitエディターにします。

```bash
git config --global core.editor "code --wait"
```

参照:

* [Git コミットメッセージの書き方](https://chris.beams.io/posts/git-commit/)
* [Git コミットメッセージに関する注意](https://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html)
* [コミットメッセージについて](http://who-t.blogspot.com/2009/12/on-commit-messages.html)
* [コミットメッセージの情報](https://wiki.openstack.org/wiki/GitCommitMessages#Information_in_commit_messages)
* [Gitコミットのベストプラクティス](https://medium.com/@nawarpianist/git-commit-best-practices-dab8d722de99)

## 参考資料

* [Git](https://git-scm.com/) `--local-branching-on-the-cheap`
* [AzureDevOps](https://azure.microsoft.com/en-us/services/devops/)
* [The GitHub Hello World](https://guides.github.com/activities/hello-world/)
* [CSE Git 詳説](git-guidance/README.md)では、[CSE](../CSE.md) プロジェクトの一部としてGitを使用する方法の詳細を詳しく説明しています。
* [GitHub-リポジトリから機密データを削除する](https://help.github.com/articles/removing-sensitive-data-from-a-repository/)
* [Gitの仕組み Pluralsightコース](https://www.pluralsight.com/courses/how-git-works)
* [Gitをマスターする Pluralsightコース](https://www.pluralsight.com/courses/master-git)
