# マージ戦略

線形または非線形のコミット履歴が必要かどうかに同意します。両方のアプローチには長所と短所があります。

* 線形のメリット: [乱雑な git 履歴を避け、線形履歴を使用する](https://dev.to/bladesensei/avoid-messy-git-history-3g26)
* 線形のデメリット: [Git リベースの使用をやめるべき理由](https://medium.com/@fredrikmorken/why-you-should-stop-using-git-rebase-5552bee4fed1)

## 非線形コミット履歴へのアプローチ

`topic` に `main` マージする。

```md
  A---B---C topic
 /         \
D---E---F---G---H main

git fetch origin
git checkout main
git merge topic
```

## 線形コミット履歴を実現するための2つのアプローチ

### メインにマージする前にトピックブランチをリベースする

`main` に `topic` をマージする前に、`main` ブランチを `topic` にリベースします。

```bash
          A---B---C topic
         /         \
D---E---F-----------G---H main

git checkout main
git pull
git checkout topic
git rebase origin/main
```

Create a PR topic --> main in Azure DevOps and approve using the squash merge option
AzureDevOpsで topic --> main のPRを作成し、squash マージオプションを使用して承認します

### スカッシュがメインにマージされる前にトピックブランチをリベースする

[スカッシュマージ](https://docs.microsoft.com/en-us/azure/devops/repos/git/merging-with-squash?view=azure-devops)は、プルリクエストを完了したときにトピックブランチのGit履歴を凝縮できるマージオプションです。`main` の履歴に`topic` の各コミットを追加する代わりに、スカッシュマージはすべてのファイル変更を取得し、それらを`main`の単一の新しいコミットに追加します。

```bash
          A---B---C topic
         /
D---E---F-----------G---H main
```

Create a PR topic --> main in Azure DevOps and approve using the squash merge option
AzureDevOpsで topic --> main のPRを作成し、squash マージオプションを使用して承認します
