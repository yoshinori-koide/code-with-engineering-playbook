# 貿易調査：GitOps

実施者: テスとジェフ

バックログ作業項目: #21672

意思決定者: ウォレス、チーム全体

## 概要

メモリについては、インフラストラクチャをコードとして使用するクラウドネイティブアプリケーションを作成します。反映されるプルリクエストインフラストラクチャの変更を通じて、継続的デプロイにGitOpsを使用します。

全体として、2つのオプションのうち、1つはより単純で、このプロジェクトの要件を満たすと思われる方法で対象を絞っています。もう1つは同じことを行いますが、追加の構成とセットアップの価値がある場合とない場合がある追加機能があります。

### 評価基準

1. レポスタイル: モノvsマルチ
1. ポリシーの施行
2. デプロイ方法
3. デプロイ監視
4. アドミッションコントロール
5. Azureドキュメントの可用性
6. 保守性
7. 成熟
8. ユーザーインターフェース

## ソリューション

### Flux

[Flux](https://toolkit.fluxcd.io/)は、Waveworksによって作成されたツールであり、KubernetesのAPI拡張システム上に構築され、マルチテナンシーをサポートし、Prometheusなどの一般的なツールとシームレスに統合されます。

#### Flux 許容基準評価

1. レポスタイル：モノ対マルチ
   - Fluxはv2の時点で両方をサポートしています
1. ポリシーの施行
   - [Azureポリシーはプレビュー中です](https://docs.microsoft.com/en-us/azure/azure-arc/kubernetes/use-azure-policy)
1. デプロイ方法
   - HelmControllerを使用してHelm [リリース](https://toolkit.fluxcd.io/guides/helmreleases/)を定義します
   - [Kustomization](https://toolkit.fluxcd.io/get-started/#deploy-podinfo-application)でデプロイについて解説
2. デプロイ監視
   - Fluxは、Prometheusと連携して、デプロイ監視とGrafanaダッシュボードを実行します
3. アドミッションコントロール
   - Fluxは、KubernetesのRBACを使用して同期権限をロックダウンします。
   - [サービスアカウントを使用して、イメージプルシークレットにアクセスする](https://docs.fluxcd.io/en/1.21.1/faq/#how-do-i-give-flux-access-to-an-image-registry)
4. Azureドキュメントの可用性
   - ヘルム演算子を使用すると、より良い
5. 保守性
   - gitリポジトリのYAMLファイルを介して管理する
6. 成熟
   - [v2はGitHubでApacheライセンスで公開](https://github.com/fluxcd/flux2)おり、Helm v3で動作し、今日からのPRコミットがあります。
   - 945スター、94フォーク
7. ユーザーインターフェース
   - CLI、最もシンプルな軽量オプション

呼び出すその他の機能（Webサイトで詳細を参照）

- Fluxはプルベースの展開のみをサポートします。つまり、オペレーターとペアリングする必要があります
- Fluxは、同期のために通知を送信したり、Webhookを受信したりできます
- ヘルス評価
- 依存関係の管理
- 自動展開
- ガベージコレクション
- コミット時にデプロイ

#### バリエーション

##### コントローラー

両方のコントローラーオプションはオプションです。

Helm Controllerは、公開するヘルムアーティファクトを追加でフェッチします。下の図を参照してください。

Kustomize Controllerは、状態と継続的デプロイメントを管理します。

これは別のトレードス​​タディであるため、ここで使用するコントローラーを決定することはしませんが、HelmはFluxのドキュメント内でより広くドキュメント化されていることに注意してください。

##### Flux v1

[Flux v1](https://github.com/fluxcd/flux)はメンテナンスモードのみであり、使用しないでください。したがって、このセクションでは、v1オプションを有効なオプションとは見なしません。

##### GitOps Toolkit

Flux v2 は[GitOps Toolkit](https://toolkit.fluxcd.io/components/)の上に構築されていますが、[GitOps Toolkit](https://toolkit.fluxcd.io/components/)を単独で使用することは評価していません。これは、独自のCDシステムを作成する場合であり、これは私たちが望んでいることではありません。

### Helm Charts を用いた ArgoCD 

ArgoCDは、Kubernetes用の宣言型のGitOpsベースの継続的デリバリー（CD）ツールです。

#### ヘルム受け入れ基準評価を伴うArgoCD

1. レポスタイル：モノ対マルチ
   - ArgoCDは両方をサポートします
1. ポリシーの施行
   - [Azureポリシーはプレビュー中です](https://docs.microsoft.com/en-us/azure/azure-arc/kubernetes/use-azure-policy)
1. デプロイ方法
   - [Helm](https://argo-cd.readthedocs.io/en/stable/user-guide/helm/) Chartを使用してデプロイする
   - [Kustomize](https://argo-cd.readthedocs.io/en/stable/user-guide/kustomize/)を使用して、Helmリリーステンプレートにポストレンダリングを適用します。
2. デプロイ監視
   - Argo CDは、デプロイメント監視用に2セットの[Prometheus](https://argo-cd.readthedocs.io/en/stable/operator-manual/metrics/)メトリック（アプリケーションメトリックとAPIサーバーメトリック）を公開します。
3. アドミッションコントロール
   - ArgoCDはRBAC機能を使用します。 [RBAC](https://argo-cd.readthedocs.io/en/stable/operator-manual/rbac/)は、SSO構成または1人以上のローカルユーザーのセットアップが必要です。SSOまたはローカルユーザーを構成すると、追加のRBACロールを定義できます
   - Argo CDには独自のユーザー管理システムがなく、組み込みのユーザー管理者は1人だけです。管理者ユーザーはスーパーユーザーであり、システムへの無制限のアクセス権があります
   - [承認はJWTトークンを介して処理](https://argo-cd.readthedocs.io/en/stable/operator-manual/security/#authentication)され、JWTトークン内のグループクレームをチェックします
4. Azureドキュメントの可用性
   - ArgoにはAzureADに関する[ドキュメント](https://argo-cd.readthedocs.io/en/stable/operator-manual/user-management/microsoft/)がある。
5. 成熟
   - 今日からPRコミットがあります
   - 5,000の星、1,100のフォーク
6. 保守性
   - GitOpsを使用して管理できます
7. ユーザーインターフェース
   - ArgoCDにはGUIがあり、クラスター間で使用できます

呼び出すその他の機能（Webサイトで詳細を参照）

- ArgoCDは、継続的デリバリーのためにプルモデルとプッシュモデルの両方をサポートします
- Argoは通知を送信できますが、別のツールが必要です
- ArgoはWebhookを受信できます
- ヘルス評価
- 潜在的にはるかに便利なマルチテナンシーツール。複数のプロジェクトを管理し、それらをチームにマッピングします。
- SSO 統合
- ガベージコレクション

![Architecture](https://argo-cd.readthedocs.io/en/stable/assets/argocd_architecture.png)

## 結果

このセクションには、各評価基準に対して各ソリューションが評価された表が含まれている必要があります。

| ソリューション | リポジトリ形式  | ポリシーの施行 | デプロイ方式       | デプロイ監視 | 権限管理 | Azure Doc              | 保守性       | 成熟性                                  | UI                                 |
| -------- | ----------- | --------------------- | ----------------------------- | --------------------- | ----------------- | ---------------------- | --------------------- | ----------------------------------------- | ---------------------------------- |
| Flux     | モノ、マルチ | Azure Policy プレビュー | Helm, Kustomize               | Prometheus, Grafana   | RBAC              | Azure上にあり           | Git の YAML      | 945 スター, 94 フォーク, 現在メンテ中 | CLI                                |
| ArgoCD   | モノ、マルチ | Azure Policy プレビュー | Helm, Kustomize, KSonnet, ... | Prometheus, Grafana   | RBAC              | Azure上にはない | Git の manifests | 5,000 スター, 1,100 フォーク                  | GUI, マルチクラスターも同じUIで管理 |

## 決断

ArgoCDはより機能が豊富で、より多くのシナリオをサポートし、ツールベルトに入れるためのより良いツールになります。そのため、この時点でArgoCDを使用することにしました。

## References

1. [GitOps](https://www.gitops.tech/#:~:text=What%20is%20GitOps?%20GitOps%20is%20a%20way%20of,familiar%20with,%20including%20Git%20and%20Continuous%20Deployment%20tools.)
1. [執行](https://docs.microsoft.com/en-us/azure/azure-arc/kubernetes/use-azure-policy)
1. [モニタリング](https://docs.microsoft.com/en-us/azure/azure-monitor/insights/container-insights-onboard)
1. [ポリシー](https://docs.microsoft.com/en-us/azure/governance/policy/concepts/policy-for-kubernetes)
1. [デプロイ](https://docs.microsoft.com/en-us/azure/azure-arc/kubernetes/use-gitops-with-helm)
2. [Azure DevOpsでArgoCDを使用してプッシュする](https://www.linkedin.com/pulse/azure-devops-gitops-aks-argocd-ajay-vikram-singh?articleId=6677601917633355776)
