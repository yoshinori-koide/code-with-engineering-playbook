# mkdocsとmkdocs-materialに基づいてドキュメント用の静的Webサイトを作成する方法

[MkDocs](https://www.mkdocs.org/)は、生のマークダウンファイルから静的Webサイトを作成するために構築されたツールです。他の選択肢には、[Sphinx](https://www.sphinx-doc.org/en/master/)、および[Jekyll](https://jekyllrb.com/)が含まれます。

MkDocsを使用して、[GitHubリポジトリ](https://github.com/microsoft/code-with-engineering-playbook)のコンテンツから[CSEコード-WithEngineeringPlaybook](https://microsoft.github.io/code-with-engineering-playbook/)静的Webサイトを作成しました。次に、それを[GitHub Pages](https://pages.github.com/)にデプロイしました。

以下の理由から、MkDocsが適切な選択であることがわかりました。

1. セットアップは簡単で、バニラバージョンでも見栄えがします。
2. これは、Playbookにすでにあるマークダウンでうまく機能します。
3. このPlaybookの多くの寄稿者に優しいPythonスタックを使用しています。

比較のために、Sphinxは主にrestructured-text（rst）形式からドキュメントを生成し、JekyllはRubyで記述されています。

MkDocs Webサイトをセットアップするために必要な主な資産は、次のとおりです。

1. [Playbookにある](https://github.com/microsoft/code-with-engineering-playbook/blob/main/mkdocs.yml) ```mkdocs.yaml```と同様のファイル。これは、Webサイトの外観、ナビゲーション、使用されるプラグインなどを定義する構成ファイルです。
2. ```docs``` ドキュメントのソースファイルを含む（ディレクトリのデフォルト値）という名前のフォルダ。
3. [Playbookのこれ](https://github.com/microsoft/code-with-engineering-playbook/blob/main/.github/workflows/mkdocs.yml)と同様の、Webサイトを自動的に生成するための[Githubアクション](https://docs.github.com/actions/learn-github-actions/understanding-github-actions)（たとえば、mainへのすべてのコミット時）。
4. Webサイトのビルドフェーズで使用されるプラグインのリスト。[ここで](https://github.com/microsoft/code-with-engineering-playbook/blob/main/requirements-docs.txt)指定しました。そして、これらは私たちが使用したプラグインです：
    - [MkDocs のマテリアル](https://squidfunk.github.io/mkdocs-material/): マテリアルデザインの外観とユーザーエクスペリエンス。
    - [pymdown-extensions](https://facelessuser.github.io/pymdown-extensions/): マークダウンベースのコンテンツの外観を改善します。
    - [mdx_truly_sane_lists](https://github.com/radude/mdx_truly_sane_lists):  Playbookに既にあるドキュメント全体をリファクタリングすることなく、リストのインデントレベルを定義します。

ローカルでのセットアップは非常に簡単です。詳細については、[MkDocs入門](https://www.mkdocs.org/getting-started/)を参照してください。

ウェブサイトを公開するために、[ウェブサイトをGithubページとして保存するためのGithubとの良好な統合](https://www.mkdocs.org/user-guide/deploying-your-docs/)があります。

## 追加のリンク

- [MkDocsプラグイン](https://github.com/mkdocs/mkdocs/wiki/MkDocs-Plugins)
- [最高のMkDocsプラグインとカスタマイズ](https://chrieke.medium.com/the-best-mkdocs-plugins-and-customizations-fc820eb19759)
