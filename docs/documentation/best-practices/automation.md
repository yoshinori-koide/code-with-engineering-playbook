# ドキュメントを自動化に置き換える

コードの実行に必要なフレームワークの適切なバージョンを使用して開発マシンをセットアップする方法、エディターを使用してアプリケーションを開発するのに役立つ拡張機能、またはアプリケーションを起動およびデバッグするようにエディターを構成する方法を文書化できます。可能であれば、代わりにツールのインストールやアプリケーションの起動などを自動化する手段を提供することをお勧めします。

いくつかの例を以下に示します。

## Visual Studio Code の開発コンテナ

[Visual Studio Code Remote - Containers 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers)を使用すると、Dockerコンテナーをフル機能の開発環境として使用できます。コンテナー内の（またはコンテナーにマウントされた）任意のフォルダーを開いて、Visual Studio Codeの全機能セットを利用できます。

追加情報：[コンテナ内での開発](https://code.visualstudio.com/docs/remote/containers)。

## Visual Studio Code で構成とタスクを起動する

[起動構成](https://code.visualstudio.com/Docs/editor/debugging#_launch-configurations)を使用すると、デバッグセットアップの詳細を構成して保存できます。

スクリプトを実行してプロセスを開始するように[タスク](https://code.visualstudio.com/Docs/editor/tasks)を構成できるため、コマンドラインを入力したり新しいコードを記述したりすることなく、これらの既存のツールの多くをVSCode内から使用できます。
