# リポジトリ間でWikiを同期する方法

これは、ProjectWikiを別のリポジトリにミラーリングするためのクイックガイドです。

```bash
# Clone the wiki
git clone <source wiki repo url>

# Add mirror repository as a remote
cd <source wiki repo working folder>
git remote add mirror <mirror repo that must already exist>
```

これで、同期するたびに、以下を実行して、ソースwikiリポジトリから最新のものを取得します。

```bash
# Get everything
git pull -v
```

> **警告**: プルの出力に「FromsourcerepoURL」が表示されていることを確認してください。これにミラーリポジトリのURLが表示されている場合は、トラッキングをリセットするのを忘れています。 `git branch -u origin/wikiMaster`を実行して続行します。

次に、これを実行してミラーリポジトリにプッシュし、ブランチをリセットしてソースリポジトリを再度追跡します。

```bash
# Push all branches up to mirror remote
git push -u mirror

# Reset local to track source remote
git branch -u origin/wikiMaster

```

実行すると、出力は次のようになります。

```powershell
PS C:\Git\MyProject.wiki> git pull -v
POST git-upload-pack (909 bytes)
remote: Azure Repos
remote: Found 5 objects to send. (0 ms)
Unpacking objects: 100% (5/5), done.
From https://.....  wikiMaster -> origin/wikiMaster
Updating 7412b94..a0f543b
Fast-forward
 .../dffffds.md | 4 ++++
 1 file changed, 4 insertions(+)


PS C:\Git\MyProject.wiki> git push -u mirror
Enumerating objects: 9, done.
Counting objects: 100% (9/9), done.
Delta compression using up to 8 threads
Compressing objects: 100% (5/5), done.
Writing objects: 100% (5/5), 2.08 KiB | 2.08 MiB/s, done.
Total 5 (delta 4), reused 0 (delta 0)
remote: Analyzing objects... (5/5) (6 ms)
remote: Storing packfile... done (48 ms)
remote: Storing index... done (59 ms)
To https://......
   7412b94..a0f543b  wikiMaster -> wikiMaster
Branch 'wikiMaster' set up to track remote branch 'wikiMaster' from 'mirror'.


PS C:\Git\MyProject.wiki> git branch -u origin/wikiMaster
Branch 'wikiMaster' set up to track remote branch 'wikiMaster' from 'origin'.
```
