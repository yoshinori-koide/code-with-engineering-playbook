# Gitの紹介にGitLFSとVFSを使用する

**Git LFS** と **VFS for Git** は、（大きな）バイナリファイルと大きなソースツリーでGitを使用するためのソリューションです。

## Git LFS

Gitは非常に優れており、コードなどのテキストベースのファイルの変更を追跡しますが、バイナリファイルの追跡はそれほど得意ではありません。たとえば、Photoshop画像ファイル（PSD）をリポジトリに保存すると、変更のたびに、ファイル全体が再び履歴に保存されます。これにより、Gitリポジトリの履歴が非常に大きくなり、リポジトリのクローンがますます時間がかかる可能性があります。

バイナリファイルを操作するためのソリューションは、Git LFS（またはGitラージファイルシステム）を使用することです。これはGitの拡張機能であり、個別にインストールする必要があり、LFSをサポートするリポジトリプラットフォームでのみ使用できます。たとえば、GitHub.comとAzure DevOpsは、LFSをサポートするプラットフォームです。

つまり、プレースホルダーファイルは、LFSシステムの情報とともにリポジトリに保存されます。これは次のようになります。

```shell
version https://git-lfs.github.com/spec/v1
oid a747cfbbef63fc0a3f5ffca332ae486ee7bf77c1d1b9b2de02e261ef97d085fe
size 4923023
```

実際のファイルは別のストレージに保存されます。このようにして、Gitは大きなファイルではなく、このプレースホルダーファイルの変更を追跡します。ただし、GitとGit LFSの組み合わせは、これを開発者から隠します。以前と同じように、リポジトリとファイルを操作します。

これらの大きなファイルを自分で操作する場合、Gitは引き続きこれらの大きなファイルをローカルで追跡し始めるため、Gitの履歴は自分のマシンで成長しますが、リポジトリのクローンを作成すると、実際には履歴はかなり小さくなります。したがって、大きなファイルを直接操作しない他の人にとっては有益です。

### Git LFS の長所

* すべてのファイルにエンドツーエンドの Git ワークフローを使用します
* Git LFSは、ファイルのロックをサポートして、差分できないアセットの競合を回避します
* Git LFSは、Azure DevOps Servicesで完全にサポートされています

### Git LFS の短所

* リポジトリに貢献するすべての人は、Git LFS をインストールする必要があります。
* 適切に設定されていない場合:
  * Gitは大きなファイルを説明するデータのみをダウンロードするため、GitLFSを介してコミットされたバイナリファイルは表示されません。
  * 大きなバイナリをコミットすると、完全なバイナリがリポジトリにプッシュされます
* Gitは、バイナリファイルの2つの異なるバージョンからの変更をマージすることはできません。ファイルロックはこれを軽減します
* Azureリポジトリは、Git LFSで追跡されたファイルを含むリポジトリでの SSH の使用をサポートしていません - Git LFS [認証のドキュメント](https://github.com/git-lfs/git-lfs/blob/master/docs/api/authentication.md)を参照してください。

### Git LFS のインストールと使用

[https://git-lfs.github.com](https://git-lfs.github.com) にアクセスし、そこからセットアップをダウンロードしてインストールします。

LFSを使用するリポジトリごとに、次の手順を実行する必要があります。

* リポジトリのLFSを設定します。

```shell
git lfs install
```

* どのファイルを大きなファイル（またはバイナリファイル）と見なす必要があるかを示します。例として、すべてのPhotoshopファイルを大きいと見なすには：

```shell
git lfs track "*.psd"
```

フォルダ内のファイルを示すためのよりきめ細かい方法などがあります。[Git LFS ドキュメント](https://github.com/git-lfs/git-lfs/tree/master/docs?utm_source=gitlfs_site&utm_medium=docs_link&utm_campaign=gitlfs)を参照してください。

これらのコマンドを使用するとこれらの設定を含む`.gitattribute`ファイルが作成されますが、これはリポジトリの一部である必要があります。

ここからは、標準のGitコマンドを使用してリポジトリで作業します。残りは Git と Git LFS によって処理されます。

### 一般的な LFS コマンド

Git LFS をインストールする

```bash
git lfs install       # windows
sudo apt-get git-lfs  # linux
```

他のシステムへのインストールについては、[Git LFS のインストール手順](https://github.com/git-lfs/git-lfs/wiki/Installation)を参照してください。

Git LFS で .mp4ファイルを追跡する場合:

```bash
git lfs track '*.mp4'
```

`.gitattributes`ファイルの、追跡するファイルとパターンのリストを更新します

```bash
*.mp4 filter=lfs diff=lfs merge=lfs -text
docs/images/* filter=lfs diff=lfs merge=lfs -text
```

追跡されたすべてのパターンを一覧表示します

```bash
git lfs track
```

追跡されたすべてのファイルを一覧表示します

```bash
git lfs ls-files
```

作業ディレクトリにファイルをダウンロードします

```bash
git lfs pull
git lfs pull --include="path/to/file"
```

## VFS for Git

複数のプロジェクトを含む大規模なリポジトリを想像してみてください。機能ごとに1つ。開発者は一部の機能にしか取り組んでいない可能性があるため、リポジトリ内のすべてのプロジェクトをダウンロードする必要はありません。ただし、デフォルトでは、Gitを使用すると、リポジトリのクローンを作成すると、すべてのファイル/プロジェクトがダウンロードされます。

VFS for Git（またはVirtual File System for Git）は、必要なものだけをローカルマシンにダウンロードするため、この問題を解決しますが、Windowsエクスプローラーなどでファイルシステムを見ると、すべてのフォルダーとファイルが表示されます。正しいファイルサイズを含みます。

これを機能させるには、Gitプラットフォームが GVFS をサポートしている必要があります。GitHub.comとAzureDev Opsはどちらも、これを完璧にサポートしています。

### VFS for Git のインストールと使用

MicrosoftはVFS for Gitを作成し、それをオープンソースにしました。[https://github.com/microsoft/VFSForGit](https://github.com/microsoft/VFSForGit)で見つけることができます。Windowsでのみ使用できます。

必要なインストーラーは[https://github.com/Microsoft/VFSForGit/releases](https://github.com/Microsoft/VFSForGit/releases)にあります。

リリースページには、2つの重要なダウンロードがあります。

* Git 2.28.0.0 インストーラー。これは、Git for VFS を実行するための要件です。これは、標準のGit for Windowsインストールと同じではありません。
* SetupGVFS インストーラー

それらのファイルをダウンロードして、マシンにインストールします。

リポジトリにVFS for Gitを使用できるようにするには、次のような行を含む`.gitattributes`ファイルをリポジトリに追加する必要があります。

```shell
* -text
```

VFS for Gitを使用してリポジトリをマシンに複製するには、`git`の代わりに `gvfs` を使用します。

```shell
gvfs clone [URL] [dir]
```

これが完了すると、リポジトリのコンテンツを含む `src`フォルダを含むフォルダが作成されます。これは、ビルドシステムのすべての出力をこのツリーの外に配置するために行われます。これにより、`.gitignore`ファイルの管理が容易になり、多数のファイルで Git のパフォーマンスを維持できます。

リポジトリを操作するには、以前と同じようにGitコマンドを使用します。

マシンからVFS for Gitリポジトリを削除するには、VFS プロセスが停止していることを確認し、メインフォルダーから次のコマンドを実行します。

```shell
gvfs unmount
```

これにより、プロセスが停止して登録が解除され、その後、フォルダを安全に削除できます。

### 参考文献

* [Git LFS の開始](https://git-lfs.github.com/)
* [Git LFS マニュアル](https://github.com/git-lfs/git-lfs/tree/master/docs)
* [Azure リポジトリ上での Git LFS](https://docs.microsoft.com/en-us/azure/devops/repos/git/manage-large-files?view=azure-devops)
