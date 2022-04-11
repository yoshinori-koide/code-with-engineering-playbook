# Gitガイダンス

## Gitとは何ですか？

Gitは分散バージョン管理システムです。これは、SVNやCVSとは異なり、同期に中央サーバーを使用しないことを意味します。代わりに、すべての参加者はソースコードのローカルコピーを持ち、添付された履歴は、（`HEAD`と呼ばれる）最新バージョンを構成するコミットハッシュ（各git commitコマンド間の変更のSHAハッシュ）を比較することによって同期されます。

例えば：

```plain
repo 1: A -> B -> C -> D -> HEAD
repo 2: A -> B -> HEAD
repo 3: X -> Y -> Z -> HEAD
repo 4: A -> J -> HEAD
```

Sそれらは共通の履歴を共有しているため、レポ1とレポ2はかなり簡単に同期できます。レポ4も同期できる可能性がありますが、レポ1にコミット（J、場合によってはマージコミット）を追加する必要があります。 Repo3は他のRepo3と簡単に同期できません。これらのコミットに関連するものはすべて、リポジトリのルートにあるローカルの.gitディレクトリに保存されます。

つまり、Gitを使用し、現在の状態を一意に識別する不変のファイル履歴を作成するだけで、その後の状態を共有できます。[マークルツリー](https://en.wikipedia.org/wiki/Merkle_tree)です。

Gitのインストール後に`git help`を実行すれば、すべてに対する非常に詳細な説明を見つけることができるでしょう。

## インストール

Gitはインストールする必要のあるツールセットです。[Gitをインストール](https://git-scm.com/downloads)し、[初めてのGitセットアップ](https://git-scm.com/book/en/v2/Getting-Started-First-Time-Git-Setup)に従います。

推奨されるインストールは、[Visual Studio Code用のGit Lens拡張機能](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens)です。Gitの非難アノテーションとコードレンズを介してコードの作成者を一目で視覚化し、Gitリポジトリをシームレスにナビゲートして探索し、強力な比較コマンドを介して貴重な洞察を得ることができます。

これらのコマンドを使用して、Git for Visual Studio Codeをマージの競合および差分ツールのエディターとして構成することもできます。

```cmd
git config --global user.name [YOUR FIRST AND LAST NAME]
git config --global user.email [YOUR E-MAIL ADDRESS]

git config --global merge.tool vscode
git config --global mergetool.vscode.cmd "code --wait $MERGED"

git config --global diff.tool vscode
git config --global difftool.vscode.cmd "code --wait --diff $LOCAL $REMOTE"
```

## 基本的なワークフロー

基本的なGitワークフローは次のとおりです。以下の特定の手順の詳細を確認できます。

```cmd
# pull the latest changes
git pull

# start a new feature branch based on the develop branch
git checkout -b feature/123-add-git-instructions develop

# edit some files

# add and commit the files
git add <file>
git commit -m "add basic instructions"

# edit some files

# add and commit the files
git add <file>
git commit -m "add more advanced instructions"

# check your changes
git status

# push the branch to the remote repository
git push --set-upstream origin feature/123-add-git-instructions
```

### クローニング

リポジトリに変更を加える場合は常に、最初にリポジトリのクローンを作成する必要があります。リポジトリのクローンを作成すると、すべてのリポジトリデータの完全なコピーが取得されるため、ローカルで作業できます。このコピーには、プロジェクトのすべてのファイルとフォルダーのすべてのバージョンが含まれています。

```cmd
git clone https://github.com/username/repo-name
```

リポジトリのクローンを作成する必要があるのは、最初のときだけです。後続のブランチの前に、`git pull` を使用してリモートリポジトリからの変更を同期できます。

### ブランチ

メインブランチ（例: `develop` ）にピアレビューされていないコードを追加しないようにするために、通常は機能ブランチで作業し、プルリクエストを使用してこれらをメイントランクにマージします。多くの場合、リポジトリの`main`または`develop`ブランチがロックされているため、プルリクエストなしで変更を加えることはできません。したがって、ローカル/機能の作業用に別のブランチを作成して、このブランチでの変更を作業および追跡できるようにすると便利です。

最新の変更をプルし、トランク（この場合 `develop`）に基づいて作業用の新しいブランチを作成します。

```cmd
git pull
git checkout -b feature/feature-name develop
```

いつでも、作業をコミットまたは隠している限り、`git checkout <branch>`でブランチ間を移動できます。ブランチの名前を忘れた場合は、すべてのブランチを一覧表示するために`git branch --all`を使用します。

### コミット

作業の損失を避けるために、小さなチャンクで頻繁にコミットすることをお勧めします。これにより、問題を発見した場合に最後の変更のみを元に戻すことができ、加えられた変更とその理由を正確に説明することもできます。

1. ブランチに変更を加える

2. 変更されたファイルを確認する

    ```cmd
    > git status
    On branch feature/271-basic-commit-info
    Changes not staged for commit:
      (use "git add <file>..." to update what will be committed)
      (use "git restore <file>..." to discard changes in working directory)
            modified:   source-control/git-guidance/readme.md
    ```

3. コミットに含めたいファイルを追跡します。変更されたすべてのファイルを追跡するには：

    ```cmd
    git add --all
    ```

   または、特定のファイルのみを追跡するには：

    ```cmd
    git add source-control/git-guidance/readme.md
    ```

4. 説明的な[コミットメッセージ](../README.md#commit-best-practices)を使用して、ローカルブランチに変更をコミットします

    ```cmd
    git commit -m "add basic git instructions"
    ```

### プッシュ

作業が完了したら、以下を使用してリモートリポジトリのブランチに変更をプッシュします。

```cmd
git push
```

初めてプッシュするときは、最初に次のようにアップストリームブランチを設定する必要があります。最初のプッシュの後、-set-upstreamパラメーターとブランチ名は不要になります。

```cmd
git push --set-upstream origin feature/feature-name
```

機能ブランチがリモートリポジトリにプッシュされると、コードにアクセスできるすべてのユーザーに開示されます。

### マージ

[CSE](../../CSE.md)では、プルリクエストを使用してコードをメインリポジトリにマージし、最終製品のすべてのコードが[コードレビューされていること](../../code-reviews/README.md)を確認することをお勧めします

[Azure DevOps](https://docs.microsoft.com/en-us/azure/devops/repos/git/pull-requests?view=azure-devops), [GitHub](https://docs.github.com/en/github/collaborating-with-issues-and-pull-requests/creating-a-pull-request)、GitHub 、およびその他の同様のツールのプルリクエスト（PR）プロセスにより、PRの開始、PRの確認、およびPRのマージの両方が簡単になります。

#### マージの競合

複数の人が同じファイルに変更を加えた場合、マージする前に、発生した競合を解決する必要がある場合があります。

```cmd
# check out the develop branch and get the latest changes
git checkout develop
git pull

# check out your branch
git checkout <your branch>

# merge the develop branch into your branch
git merge develop

# if merge conflicts occur, above command will fail with a message telling you that there are conflicts to be solved

# find which files need to be resolved
git status
```

どのファイルに競合があるかを示すインタラクティブなプロセスを開始できます。時々あなたはそれがdevで変更されたファイルを削除しました。または、別の開発者が変更を加えたファイルの一部の行に変更を加えました。前述のインストール手順を実行した場合、Visual Studio Codeはマージツールとしてセットアップされます。[kdiff3](https://github.com/KDE/kdiff3) のようなマージツールを使用することもできます。編集の競合が発生すると、プロセスによってVisual Studio Codeが自動的に開き、競合する部分が緑と青で強調表示されます。次のいずれかを選択できます。

* 変更を受け入れる (current)
* devブランチからの変更を受け入れる (incoming)
* それらの両方を受け入れて、コードを修正します (おそらく必要です)

```text
Here are lines that are either unchanged from the common
ancestor, or cleanly resolved because only one side changed.
<<<<<<< yours:sample.txt
Conflict resolution is hard;
let's go shopping.
=======
Git makes conflict resolution easy.
>>>>>>> theirs:sample.txt
And here is another line that is cleanly resolved or unmodified
```

このプロセスが完了したら、ビルド、チェック、テストを実行して結果をテストし、このマージされた結果を検証してください。

```cmd
# conclude the merge
git merge --continue

# verify that everything went ok
git log

# push the changes to the remote branch
git push
```

他の競合が表示されない場合は、PRをマージして、ブランチを削除できます。変更を1つのコミットに減らすために`squash`使用します。これにより、コミット履歴を許容可能なサイズ内に収めることができます。

### 変更のスタッシング

作業ディレクトリにコミットされていない変更があるが、別のブランチで作業したい場合、`git stash`が非常に便利です。`git stash`を実行し、コミットされていない作業を保存して、HEADコミットに戻すことができます。`git stash pop` を実行すると、保存された変更を取得できます。

```cmd
git stash
…
git stash pop
```

または、現在の状態を新しいブランチに移動することもできます。

```cmd
git stash branch <new_branch_to_save_changes>
```

### 失われたコミットの回復

例えば コミットが押しつぶされた場所で`git rebase`したのを元に戻したい、などのようにコミットを「失った」場合、 `git reflog` コマンドを使用してコミットを見つけることができます。

```cmd
git reflog
```

次に、reflog参照（HEAD@{}）を使用して、リベースの前に特定のコミットにリセットできます。

```cmd
git reset HEAD@{2}
```

## リモートの管理

ローカルのgitリポジトリには、1つ以上のバッキングリモートリポジトリを含めることができます。`git remote`を使用してリモートリポジトリを一覧表示できます-デフォルトでは、クローンを作成したリモートリポジトリは origin と呼ばれます
A local git repository can have one or more backing remote repositories. You can list the remote repositories using `git remote` - by default, the remote repository you cloned from will be called origin

```cmd
> git remote -v
origin  https://github.com/microsoft/code-with-engineering-playbook.git (fetch)
origin  https://github.com/microsoft/code-with-engineering-playbook.git (push)
```

### フォークでの作業

複数のリモートを設定できます。これは、たとえば、フォークされたバージョンのリポジトリを操作する場合に役立ちます。フォークを操作するときにアップストリームリモートを設定してリポジトリを同期する方法の詳細については、GitHubの[フォークの操作に関するドキュメント](https://docs.github.com/en/github/collaborating-with-issues-and-pull-requests/working-with-forks)を参照してください。

### リポジトリの名前が変更された場合のリモートの更新

名前の変更など、リポジトリが何らかの方法で変更された場合、またはHTTPSとSSHを切り替えたい場合は、リモートを更新する必要があります

```cmd
# list the existing remotes
> git remote -v
origin  https://hostname/username/repository-name.git (fetch)
origin  https://hostname/username/repository-name.git (push)

# change the remote url
git remote set-url origin https://hostname/username/new-repository-name.git

# verify that the remote URL has changed
> git remote -v
origin  https://hostname/username/new-repository-name.git (fetch)
origin  https://hostname/username/new-repository-name.git (push)
```

## 変更のロールバック

### コミットの取り消しと削除

コミットを「元に戻す」には、次の2つのコマンドを実行します。`git revert`および`git reset`。`git revert`は、コミット履歴から`git reset`でコミットを完全に削除できるようにしながら、コミットを元に戻す新しいコミットを作成します。

> シークレット/キーをコミットした場合は、`git reset`でコミット履歴からそれらを削除します！

最新のコミットを **削除する**には、`HEAD~` を使用します。

```bash
git reset --hard HEAD~1
```

コミットを削除して特定のコミットに戻すには、それぞれのコミットIDを使用します。

```bash
git reset --hard <sha1-commit-id>
```

不要なコミットを削除した後、 `force`を使用してプッシュします。

```bash
git push origin HEAD --force
```

コミットを元に戻すためのインタラクティブなリベース：

```bash
git rebase -i HEAD~N
```

上記のコマンドは、エディター（vimなど）でインタラクティブセッションを開き、最後のN個のコミットを古いものから新しいものへと並べ替えます。コミットを元に戻すには、コミットの対応する行を削除してファイルを保存します。Gitは、ファイルにリストされている順序でコミットを書き換えます。1つ（または複数）のコミットが削除されたため、そのコミットは履歴の一部ではなくなります。

リベースを実行すると、履歴がローカルで変更されます。`force`を使用すると、コミットを削除せずに変更をリモートにプッシュできます。

## サブモジュールの使用

サブモジュールは、より複雑な展開や開発のシナリオで役立ちます。

リポジトリにサブモジュールを追加する

```bash
git submodule add -b master <your_submodule>
```

サブモジュールを使用してリポジトリを初期化してプルします。

```bash
git submodule init
git submodule update --init --remote
git submodule foreach git checkout master
git submodule foreach git pull origin
```

## 画像、ビデオ、その他のバイナリコンテンツの操作

大きな画像、ビデオ、コンパイルされたコードなど、頻繁に変更されるバイナリファイルをgitリポジトリにコミットしないでください。バイナリコンテンツはテキストコンテンツのように区別されないため、クローンを作成したり、リポジトリからプルしたりすると、バイナリファイルの各リビジョンがプルされる可能性があります。

この問題の1つの解決策は `Git LFS (Git Large File Storage)` … 大きなファイルをバージョン管理するためのオープンソースのGit拡張機能です。Git LFSの詳細については、[Git LFS および VFS ドキュメント](git-lfs-and-vfs.md)を参照してください。

## 大規模なリポジトリでの作業

すべてのファイルを必要としない非常に大きなリポジトリで作業する場合は`VFS for Git`、Gitリポジトリの下のファイルシステムを仮想化するオープンソースのGit拡張機能を使用できます。これにより、通常の作業ディレクトリで作業しているように見えます。 VFS for Gitは、必要に応じてオブジェクトのみをダウンロードします。GitのVFSの詳細については、[Git LFS および VFS ドキュメント](git-lfs-and-vfs.md)を参照してください。

## ツール

* Visual Studio Codeは、gitコマンドが組み込まれたクロスプラットフォームの強力なソースコードエディターです。Visual Studio Codeエディター内で、差分の確認、変更のステージング、コミットの作成、gitリポジトリーへのプルとプッシュを行うことができます。ドキュメントについては、[Visual Studio Code Git サポート](https://code.visualstudio.com/docs/editor/versioncontrol#_git-support)を参照してください。

* [GUI クライアント](https://git-scm.com/downloads/guis/)に依存する代わりに、シェル/ターミナルを使用してGitコマンドを操作します。

* Windowsで作業している場合、[posh-git](https://github.com/dahlbyk/posh-git)はGitに最適なPowerShell環境です。もう1つのオプションは、[Windows用のGit bash](http://www.techoism.com/how-to-install-git-bash-on-windows/)を使用することです。Linux / Macでは、gitをインストールし、お気に入りのシェル/ターミナルを使用します。
