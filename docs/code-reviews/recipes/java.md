# Javaコードレビュー

※ オリジナル: https://microsoft.github.io/code-with-engineering-playbook/code-reviews/recipes/java/

## Javaスタイルガイド

[CSE](../../CSE.md)開発者は通常、 [Google Javaスタイルガイド](https://google.github.io/styleguide/javaguide.html)に従います。

## コード分​​析/リンティング

一貫したスタイルにより、コードベースの可読性と保守性が向上すると確信しています。したがって、一貫性とスタイルのルールを適用するためにアナライザーをお勧めします。

[Azure Java SDKで使用されているのと同じ構成](https://github.com/Azure/azure-sdk-for-java/blob/master/eng/code-quality-reports/src/main/resources/checkstyle/checkstyle.xml)を使用して[Checkstyle](https://github.com/checkstyle/checkstyle)を使用します。

[FindBugs](http://findbugs.sourceforge.net/)と[PMD](https://pmd.github.io/)も一般的に使用されています。

## 自動コードフォーマット

Eclipseおよびその他のJavaIDEは、自動コードフォーマットをサポートしています。Mavenを使用している場合、一部の開発者は[formatter-maven-plugin](https://github.com/revelc/formatter-maven-plugin)も使用します。

## ビルド検証

チームメンバーが標準に準拠していないコードをgitリポジトリにマージしないように、CIでコードスタイルとルールを適用することが重要です。Azure DevOpsを使用してビルドする場合、Azure DevOpsは、すべてのビルドの一部として[PMD](https://pmd.github.io/)、[Checkstyle](https://checkstyle.sourceforge.io/)、および[FindBugs](http://findbugs.sourceforge.net/)コード分析ツールを使用する[Maven](https://docs.microsoft.com/azure/devops/pipelines/tasks/build/maven?view=azure-devops)および[Gradle](https://docs.microsoft.com/azure/devops/pipelines/tasks/build/gradle?view=azure-devops)ビルドタスクをサポートします。

3つの分析ツールすべてを有効にしたMavenビルドタスクのyamlの例を次に示します。

```yaml
    - task: Maven@3
    displayName: 'Maven pom.xml'
    inputs:
        mavenPomFile: '$(Parameters.mavenPOMFile)'
        checkStyleRunAnalysis: true
        pmdRunAnalysis: true
        findBugsRunAnalysis: true
```

3つの分析ツールすべてを有効にしたGradleビルドタスクのyamlの例を次に示します。

```yaml
    - task: Gradle@2
    displayName: 'gradlew build'
    inputs:
        checkStyleRunAnalysis: true
        findBugsRunAnalysis: true
        pmdRunAnalysis: true
```

## コードレビューチェックリスト

[コードレビューチェックリスト](../process-guidance/reviewer-guidance.md)に加えて、これらのJava固有のコードレビュー項目も探す必要があります

* [ ] プロジェクトはLambdaを使用してコードをクリーンにしますか？
* [ ] 依存性注入（DI）は使用されていますか？正しく設定されていますか？
* [ ] コードでSpringBootを使用している場合、`@Autowire` の代わりに`@Inject`を使用していますか？
* [ ] コードは例外を正しく処理しますか？
* [ ] [Azul Zulu OpenJDK](https://docs.microsoft.com/en-us/java/azure/jdk/java-jdk-install?view=azure-java-stable) が使用されていますか？
* [ ] ビルド自動化およびパッケージ管理ツール（GradleまたはMaven）が使用されていますか？