# データサイエンスパイプライン

Azure DevOps ではコードレビューアがJupyter Notebookに直接コメントすることを許可していないため、データサイエンティスト（DS）は、これらのファイルをコミットしてリポジトリにプッシュする前に、ノートブックをスクリプトに変換する必要があります。

このドキュメントは、Azure DevOpsでこのプロセスを自動化することを目的としているため、DS はローカルで何も実行する必要がありません。

## 問題文

データサイエンスリポジトリのフォルダ構造は次のとおりです。

```bash

    .
    ├── notebooks
    │   ├── Machine Learning Experiments - 00.ipynb
    │   ├── Machine Learning Experiments - 01.ipynb
    │   ├── Machine Learning Experiments - 02.ipynb
    │   ├── Machine Learning Experiments - 03.ipynb
    └── scripts
       ├── Machine Learning Experiments - 00.py
       ├── Machine Learning Experiments - 01.py
       ├── Machine Learning Experiments - 02.py
       └── Machine Learning Experiments - 03.py

```

プルリクエストのレビュー担当者がノートブックにコメントを追加できるようにするには、Pythonファイルが必要です。Pythonスクリプトにコメントを追加でき、これらのコメントをノートブックに適用します。

コミットにファイルを追加する前にこのプロセスを手動で実行する必要があるため、この手動プロセスはエラーが発生しやすくなります。たとえば、ノートブックを作成する場合は、そこからスクリプトを生成しますが、後で変更を加えて、変更します。

## 解決方法

これを回避する1つの方法は、コミットからリポジトリにスクリプトを作成することです。このドキュメントでは、このプロセスについて説明します。

次の手順でパイプラインをリポジトリに追加して、`ipynb`ファイルで実行できます。

1. *[プロジェクト設定]* -> *[リポジトリ]* -> *[セキュリティ]* -> *[ユーザー権限]* に移動します
1. *ユーザー*に*ビルドサービス*を追加して、*貢献する*権限を追加します
    ![Contribute](assets/repository-properties.png)
1. 新しいパイプラインを作成します。

新しく作成されたパイプラインに、以下を追加します。

1. ipynbファイルで実行するトリガー：

    ```yml
    trigger:
      paths:
      include:
        - '*.ipynb'
        - '**/*.ipynb'
    ```

1. プールをLinuxとして選択します。

    ```yml
    pool:
      vmImage: ubuntu-latest
    ```

1. スクリプトを保存するディレクトリを設定します。

    ```yml
    variables:
      REPO_URL: # Azure DevOps URL in the format: dev.azure.com/<Organization>/<Project>/_git/<RepoName>
    ```

1. 次に、パイプラインのコアを開始します。
    1. pip をアップグレードします

    ```yml
    - script: |
        python -m pip install --upgrade pip
      displayName: 'Upgrade pip'

    ```

    1. `nbconvert` と `ipython` をインストールします。:

    ```yml
    - script: |
        pip install nbconvert ipython
      displayName: 'install nbconvert & ipython'
    ```

    1. `pandoc`をインストールします。:

    ```yml
    - script: |
        sudo apt install -y pandoc
      displayName: "Install pandoc"
    ```

    1. リポジトリへの最後のコミットでノートブックファイル（ipynb）を見つけて、スクリプト（`py`）に変換します。:

    ```yml
    - task: Bash@3
        inputs:
          targetType: 'inline'
          script: |
            IPYNB_PATH=($(git diff-tree --no-commit-id --name-only -r $(Build.SourceVersion) | grep '[.]ipynb$'))
            echo $IPYNB_PATH
            [ -z "$IPYNB_PATH" ] && echo "Nothing to convert" || jupyter nbconvert --to script $IPYNB_PATH
        displayName: "Convert Notebook to script"
    ```

    1. これらの変更をリポジトリにコミットします。:

    ```yml
    - bash: |
        git config --global user.email "build@dev.azure.com"
        git config --global user.name "build"
        git add .
        git commit -m 'Convert Jupyter notebooks' || echo "No changes to commit" && NO_CHANGES=1
        [ -z "$NO_CHANGES" ] || git push https://$(System.AccessToken)@$(REPO_URL) HEAD:$(Build.SourceBranchName)
      displayName: "Commit notebook to repository"
    ```

これで、ノートブックをコミットするときにスクリプトを生成するパイプラインができました。
