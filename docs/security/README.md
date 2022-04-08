# セキュリティ

[CSE](../CSE.md) プロジェクトに取り組んでいる開発者は、コードの安全な設計と実装のために、業界が推奨する標準的な手法に従う必要があります。これは、お客様の目的のために、エンジニアが[OWASPトップ10 Webアプリケーションセキュリティリスク](https://owasp.org/www-project-top-ten/)と、以下のリソースを使用してそれらのリスクを可能な限り軽減する方法を理解する必要があることを意味します。

アプリケーションまたは設計の評価を**開始するための迅速な方法を探している場合**は、以下の「セキュアコーディングプラクティスクイックリファレンス」ドキュメントを確認してください。このドキュメントには、適切に実行されていることを検証できる高レベルの概念の項目別チェックリストが含まれています。このチェックリストは、上記のリンク先のOWASPトップ10リストに関連する多くの一般的なエラーをカバーしており、セキュリティに最小限の労力を費やす必要があります。

## セキュリティレビューのリクエスト

アプリケーションのセキュリティレビューをリクエストするときは、[エンゲージメントのルール](rules-of-engagement.md)に精通していることを確認してください。これは、テスト用のアプリケーションを準備するのに役立つだけでなく、テストの範囲の制限を理解するのにも役立ちます。

## クイックリファレンス

- [セキュアなコーディング慣行クイックリファレンス](https://owasp.org/www-pdf-archive/OWASP_SCP_Quick_Reference_Guide_v2.pdf)
- [Webアプリケーションセキュリティクイックリファレンス](https://owasp.org/www-pdf-archive//OWASP_Web_Application_Security_Quick_Reference_Guide_0.3.pdf)
- [セキュリティマインドセット/セキュリティプログラムの作成クイックスタート](https://github.com/OWASP/Quick-Start-Guide/blob/master/OWASP%20Quick%20Start%20Guide.pdf?raw=true)

## Azure DevOps セキュリティ

- [セキュリティエンジニアリングDevSecOpsプラクティス](https://www.microsoft.com/en-us/securityengineering/devsecops)
- [Azure　DevOps データ保護の概要](https://docs.microsoft.com/en-us/azure/devops/organizations/security/data-protection?view=azure-devops)
- [Azure DevOps のセキュリティとID](https://docs.microsoft.com/en-us/azure/devops/organizations/security/about-security-identity?view=azure-devops)
- [セキュリティコード分析](https://secdevtools.azurewebsites.net/)

## DevSecOps

早い段階でプロジェクトにセキュリティを導入します。[DevSecOps セクション](../continuous-integration/dev-sec-ops/README.md)では、アプリケーションCIの一部として、セキュリティプラクティス、自動化、ツール、およびフレームワークについて説明します。

## OWASP チートシート

> 注：OWASPは、コンピューターのセキュリティ情報のゴールドスタンダードと見なされています。OWASPは、すべてのOWASPトップ10以上をカバーする広範な一連のチートシートを維持しています。以下に、より関連性の高い虎の巻の多くを要約します。すべてのチートシートを表示するには、[チートシートインデックス](https://github.com/OWASP/CheatSheetSeries/blob/master/Index.md)を確認してください。

- [アクセス制御の基本](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Access_Control_Cheat_Sheet.md)
- [攻撃対象領域の分析](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Attack_Surface_Analysis_Cheat_Sheet.md)
- [コンテンツセキュリティポリシー(CSP)](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Content_Security_Policy_Cheat_Sheet.md)
- [クロスサイトリクエストフォージェリ（CSRF）の防止](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.md)
- [クロスサイトスクリプティング（XSS）の防止](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.md)
- [暗号化ストレージ](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Cryptographic_Storage_Cheat_Sheet.md)
- [デシリアライズ](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Deserialization_Cheat_Sheet.md)
- [Docker / Kubernetes（k8s）セキュリティ](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Docker_Security_Cheat_Sheet.md)
- [入力検証](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Input_Validation_Cheat_Sheet.md)
- [キー管理](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Key_Management_Cheat_Sheet.md)
- [OSコマンドインジェクションディフェンス](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/OS_Command_Injection_Defense_Cheat_Sheet.md)
- [クエリのパラメータ化の例](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Query_Parameterization_Cheat_Sheet.md)
- [サーバー側のリクエストフォージェリ防止](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Server_Side_Request_Forgery_Prevention_Cheat_Sheet.md)
- [SQLインジェクションの防止](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.md)
- [未検証のリダイレクトと転送](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Unvalidated_Redirects_and_Forwards_Cheat_Sheet.md)
- [Webサービスセキュリティ](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Web_Service_Security_Cheat_Sheet.md)
- [XMLセキュリティ](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/XML_Security_Cheat_Sheet.md)

## 推奨ツール

プロジェクトでセキュリティを有効にするのに役立つツールのリストを確認してください。

> **注:** 一部のツールは環境に依存しないツールですが、以下のリストは、Kubernetesに焦点を当てたクラウドネイティブセキュリティを対象としています。

- 脆弱性スキャン

  - [SonarCloud](https://sonarcloud.io/)
    - ボタンをクリックするだけでAzureDevopsと統合します。
  - [Snyk](https://github.com/snyk/snyk)
  - [Trivy](https://github.com/aquasecurity/trivy)
  - [Cloudsploit](https://github.com/aquasecurity/cloudsploit)
  - [Anchore](https://github.com/anchore/anchore-engine)
  - [その他 OWASP より](https://owasp.org/www-community/Vulnerability_Scanning_Tools)
  - [スタックのすべてのレイヤーで脆弱性をチェックする必要がある理由](https://sysdig.com/blog/image-scanning-best-practices/)と、攻撃の表面積を減らすためのその他の役立つヒントをいくつかご覧ください。

- ランタイムセキュリティ

  - [Falco](https://github.com/falcosecurity/falco)
  - [Tracee](https://github.com/aquasecurity/tracee)
  - [Kubelinter](https://github.com/stackrox/kube-linter)
    - ランタイムセキュリティとして完全に認定されていない可能性がありますが、ベストプラクティスを有効にしていることを確認するのに役立ちます。

- バイナリ認証

  バイナリ認証は、Dockerレジストリレイヤーとランタイムの両方で発生する可能性があります（つまり、K8sアドミッションコントローラーを介して）。承認チェックは、イメージが信頼できる機関によって署名されていることを確認します。これは、（事前に承認された）サードパーティのイメージと内部イメージの両方で発生する可能性があります。これをさらに一歩進めると、すべてのコードがレビューおよび承認された画像で*のみ*署名が行われる必要があります。バイナリ認証は、侵害されたホスティング環境による被害と悪意のある内部関係者による被害の両方を軽減できます。

  - [Harbor](https://github.com/goharbor/harbor/)
    - [利用可能なオペレーター](https://github.com/goharbor/harbor-operator)
  - [Portieris](https://github.com/IBM/portieris)
  - [Notary](https://github.com/theupdateframework/notary)
    - Harbor は Notary を内部的に活用していることに注意してください。
  - [TUF](https://github.com/theupdateframework/tuf)

- その他のK8sセキュリティ

  - [OPA](https://github.com/open-policy-agent/opa), [Gatekeeper](https://github.com/open-policy-agent/gatekeeper)、および [Gatekeeper Library](https://github.com/open-policy-agent/gatekeeper-library/tree/master/library)
  - [cert-manager](https://github.com/jetstack/cert-manager)によって証明書のプロビジョニングと自動ローテーションを簡単にできます。
  - [Linkerdを使用して、マイクロサービス間でmTLSをすばやく有効にします](https://linkerd.io/2/features/automatic-mtls/)。

## 便利なリンク

- [非機能要件ガイダンス](../design/design-patterns/non-functional-requirements-capture-guide.md)
