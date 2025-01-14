# 継続的デリバリー

継続的デリバリーの背後にあるインスピレーションは、ユーザーと開発者により頻繁に価値のあるソフトウェアを絶えず提供することです。このreadmeに記載されている原則と実践を適用することで、リスクを軽減し、手動操作を排除し、品質と信頼性を高めることができます。

ソフトウェアの展開には、次の原則が含まれます。

1. アプリケーション（クラウドリソース、インフラストラクチャ、ハードウェア、サービスなど）のクラウド環境ランタイムをプロビジョニングおよび管理します。
1. クラウド環境全体にターゲットアプリケーションバージョンをインストールします。
1. 必要なデータを含めて、アプリケーションを構成します。s

継続的デリバリーパイプラインは、プロセスの自動化された表現であり、一貫性のある繰り返し可能な方法でこれらの原則を合理化します。

## ゴール

* 顧客と開発者にソフトウェアの変更を提供するための業界のベストプラクティスに従ってください。
* 継続的デリバリーワークフローを組み立てる際の指針とベストプラクティスの一貫性を確立します。

## 一般的なガイダンス

### リリース戦略を定義する

プロジェクトの計画段階で、リリース戦略/設計に関して開発リーダーとアプリケーションの利害関係者の間で共通の理解を確立することが重要です。この一般的な理解には、SDLC全体でのアプリケーションの展開と保守が含まれます。

#### リリース戦略の原則

Jez Humble、David Farleyによる*継続的デリバリー* では、リリース戦略を作成する際に従うべき重要な考慮事項について説明している。

* 各環境への展開とリリースを担当する当事者。
* 資産および構成管理戦略。
* 受け入れ、容量、統合、およびユーザー受け入れテストに使用できる環境の列挙、およびビルドがこれらの環境を移動するプロセス。
* 開く必要のある変更要求や付与する必要のある承認など、テスト環境および実稼働環境に展開するために従う必要のあるプロセスの説明。
* アプリケーションのデプロイ時間とランタイム構成を管理する方法、およびこれが自動デプロイメントプロセスにどのように関連するかについての説明。
* _災害後にアプリケーションの状態を回復できるようにするための災害復旧計画。アプリケーションが失敗した場合に、アプリケーションを再起動または再デプロイするには、どの手順を実行する必要がありますか。
* _本番環境のサイジングと容量計画：ライブアプリケーションはどのくらいのデータを作成しますか？いくつのログファイルまたはデータベースが必要ですか？どのくらいの帯域幅とディスク容量が必要ですか？クライアントはどのような遅延を期待していますか？
* 本番環境への初期展開はどのように機能しますか。
* 欠陥の修正と実稼働環境へのパッチの適用方法。
* データ移行を含む、実稼働環境へのアップグレードの処理方法。状態を破壊せずに、アプリケーションのアップグレードをどのように実行するか。

### アプリケーションのリリースと環境の促進

リリースマニフェストプロセスでは、コミットステージから作成されたデプロイ可能なビルドアーティファクトを取得し、テスト環境から始めて、すべてのクラウド環境にデプロイする必要があります。

テスト環境（*統合と呼ばれることもあります*）は、すべてのリリース候補に対してテストスイートが正常に完了したかどうかを検証するためのゲートとして機能します。この検証は、コードの変更を含む機能/リリースブランチから統合されたデプロイ済みリリースを検査している間、常にテスト環境で開始する必要があります。

*テスト*環境にリリースされたコード変更は、通常、メインブランチ（[トランク](https://devblogs.microsoft.com/devops/release-flow-how-we-do-branching-on-the-vsts-team/#why-trunk-based-development)を実行している場合）またはリリースブランチ（[gitflow](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow)を実行している場合）を対象としています。

#### 最初の展開

アプリケーションの最初の展開は、本番環境のような環境（*UAT*）で顧客に紹介し、フィードバックを早期に求める必要があります。UAT環境は、製品所有者の承認を取得して、最終的に本番環境へのリリースを促進するために使用されます。

#### 本番環境のような環境の基準

* 本番環境と同じオペレーティングシステムを実行します。
* 本番環境と同じソフトウェアがインストールされています。
* 実稼働と同じサイズと構成です。
* 本番環境のネットワークトポロジをミラーリングします。
* シミュレートされた本番環境のような負荷テストは、リリース後に実行され、遅延やスループットの低下を明らかにします。

#### リリースパイプラインのモデリング

テストとリリースのプロセスをモデル化して、アプリケーションエンジニアと顧客の利害関係者の間で共通の理解を確立することが重要です。具体的には、事前にプロビジョニングする必要のあるクラウド環境の数に対する期待を調整し、サインオフゲートの役割と責任を定義します。

![image](./images/example_release_flow.png)

##### リリースパイプラインモデリングの考慮事項

* アプリケーションの変更が本番環境にリリースされる前に実行する必要があるすべての段階を示します。
* すべてのリリースゲートコントロールを定義します。
* 環境ごとにリリース候補を承認する権限を持つ顧客固有のクラウドRBACグループを決定します。

#### パイプラインステージのリリース

リリースワークフロー内の段階では、最終的にアプリケーションのバージョンをテストして、受け入れ基準に従ってリリースできることを検証します。リリースパイプラインは、次の条件を考慮する必要があります。

* リリースの選択：アプリケーションテストを実行する開発者は、テスト環境にデプロイするリリースバージョンを選択できる必要があります。
* デプロイ - アプリケーションのデプロイ可能なビルドアーティファクト（*CIステージから作成*）をターゲットクラウド環境にリリースします。
* 構成 - アプリケーションは、すべての環境で一貫して構成する必要があります。この構成は、展開時に適用されます。アプリのシークレットや証明書などの機密データは、完全に管理されたPaaSキーとシークレットストア（[Key Vault](https://azure.microsoft.com/en-us/services/key-vault/)、[KMS](https://aws.amazon.com/kms/)など）でマスターする必要があります。アプリケーションで使用されるシークレットは、アプリケーション自体の内部で取得する必要があります。アプリケーションシークレットは、ランタイム環境内で公開しないでください。特に[構成管理](https://12factor.net/config)に関しては、12の要素の原則をお勧めします。
* データ移行 - ランタイム環境に必要なアプリケーションの状態やデータレコードを事前に入力します。これには、エンドツーエンドの統合テストスイートに必要なテストデータも含まれる場合があります。
* 展開スモークテスト。スモークテストでは、アプリケーションが正しい構成を指していることも確認する必要があります（たとえば、本番環境がUATデータベースを指している）。
* 手動または自動の受け入れテストシナリオを実行します。
* リリースゲートを承認して、アプリケーションバージョンをターゲットクラウド環境にプロモートします。このプロモーションには、環境の構成状態（新しい環境設定、機能フラグなど）も含める必要があります。

#### ライブリリースウォーミングアップ

リリースは、ライブと見なされてユーザートラフィックの受け入れを許可される前に、一定期間実行されている必要があります。これらのウォームアップアクティビティには、アプリケーションサーバーとデータベースが依存キャッシュを事前に入力し、すべてのサービス接続（*接続プールの割り当てなど*）を確立することが含まれる場合があります。

#### 実動前リリース

アプリケーションリリース候補は、最終的な手動/自動テスト（*容量テストを含む*）を実行するために、本番環境と同様のステージング環境に展開する必要があります。実稼働およびステージング/実稼働前のクラウド環境は、プロジェクトの開始時にセットアップする必要があります。

アプリケーションのウォームアップは、製品前のスモークテストの一部として検証された定量化された測定値である必要があります。

### ロールバックリリース

リリース戦略では、展開後に予期しない障害が発生した場合のロールバックシナリオを考慮する必要があります。

リリースのロールバックは、特に展開の結果としてデータベースレコード/オブジェクトの変更が発生した場合（*不注意または意図的に*）、注意が必要になる場合があります。バックアウトする必要のあるデータ変更がない場合は、最後の既知の製品バージョンの新しいリリース候補をトリガーし、CDパイプラインに沿ってそのリリースをプロモートするだけです。

データ変更を伴うロールバックシナリオの場合、これを軽減するためのいくつかのアプローチがあり、このガイドの範囲外です。一部には、データベースレコードのバージョン管理、データベースレコード/オブジェクトのタイムマシニングなどが含まれます。すべてのデータファイルとデータベースは、復元できるように、各リリースの前にバックアップする必要があります。このシナリオの緩和戦略は、プロジェクトによって異なります。この緩和戦略は、リリース戦略の一部としてカバーする必要があることが期待されます。

リリース戦略を設計する際に考慮すべきもう1つのアプローチは、[デプロイメントリング](https://docs.microsoft.com/en-us/azure/devops/migrate/phase-rollout-with-rings?view=azure-devops)です。このアプローチは、本番環境での変更を段階的に展開して検証することにより、エンドユーザーへのリリースの影響を制限しながら、ロールバックシナリオを簡素化します。

### ゼロダウンタイムリリース

ホットデプロイメントは、ユーザーエクスペリエンスに影響を与えることなく、ユーザーをあるリリースから別のリリースに切り替えるプロセスに従います。例として、Azureマネージドアプリサービスを使用すると、開発者は、ステージング展開スロットでアプリの変更を検証してから、本番スロットと交換できます。ソーススロットが完全にウォームアップされると（そして[自動スワップ](https://docs.microsoft.com/en-us/azure/app-service/deploy-staging-slots#configure-auto-swap)が有効になると）、AppServiceスロットのスワップも完全に自動化できます。スロットスワッピングは、技術オペレーターがスロットをスワッピング前の状態に復元した後のリリースロールバックも簡素化します。

Kubernetesは[ローリングアップデート](https://kubernetes.io/docs/tutorials/kubernetes-basics/update/update-intro/)をネイティブにサポートしています。

### ブルー - グリーン展開

Blue / Greenは、 *Blue*と*Green*と呼ばれる本番環境の2つの同一のインスタンスを実行することにより、ダウンタイムを削減するデプロイメント手法です。

これらの環境の1つだけが、特定の時間にライブの本番トラフィックを受け入れます。

![image](./images/blue_green.png)

上記の例では、ライブプロダクショントラフィックはグリーン環境にルーティングされます。アプリケーションのリリース中に、新しいバージョンは、グリーン環境とは独立して発生するブルー環境にデプロイされます。ライブトラフィックは、Blue環境のリリースの影響を受けません。テストチェックアウトの1つとして、Blue環境に対してエンドツーエンドのテストスイートを指定できます。

ユーザーを新しいアプリケーションバージョンに移行するのは、ルーター構成を変更してすべてのトラフィックをBlue環境に転送するのと同じくらい簡単です。

この手法では、ルーターをグリーンに戻すだけでよいため、ロールバックシナリオが簡素化されます。

CosmosやAzureSQLなどのデータベースプロバイダーは、データレプリケーションをネイティブにサポートして、完全に同期されたBlueGreenデータベース環境を実現します。

### カナリア リリース

カナリアリリースにより、開発チームは、新機能を本番環境にデプロイするときに、より迅速なフィードバックを収集できます。これらのリリースは、本番ノードのサブセット（ユーザーがルーティングされていない）にロールアウトされ、容量テストと機能の完全性および影響に関する初期の洞察を収集します。

![image](./images/canary_release.png)

スモークと容量のテストが完了したら、リリース候補をホストしている本番ノードにユーザーの小さなサブセットをルーティングできます。

Canaryリリースは、ユーザーを不正なアプリケーションバージョンにルーティングすることを回避できるため、ロールバックを簡素化します。

保守と監視の制御が複雑になる可能性があるため、本番環境で並行して実行されるアプリケーションのバージョン数を制限するようにしてください。

### ローコードソリューション

ローコードソリューションは、アプリケーションとプロセスへの参加を増やしました。そのため、分野を適切に組み合わせることで、開発を改善する必要があります。

こちらは、[ローコードソリューションの継続的展開](low-code-solutions/low-code-solutions.md)のガイドです。

## 参考文献

* Jez Humble、David Farleyによる [継続的デリバリー](https://www.continuousdelivery.com/)
* [継続的インテグレーションvs.継続的デリバリーvs.継続的デプロイ](https://www.atlassian.com/continuous-delivery/principles/continuous-integration-vs-delivery-vs-deployment)
* [展開リング](https://docs.microsoft.com/en-us/azure/devops/migrate/phase-rollout-with-rings?view=azure-devops)

### ツール

上記のCDのベストプラクティスに役立つ以下のツールを確認してください。

* GitOps の [Flux](https://fluxcd.io/docs/concepts/)
* [GitOpsを使用したCI/CDワークフロー](https://docs.microsoft.com/en-us/azure/azure-arc/kubernetes/conceptual-gitops-ci-cd#example-workflow)
* [Tekton](https://github.com/tektoncd) for Kubernetes ネイティブパイプライン
  * Jenkins-Xは内部でTektonを使用していることに注意してください。
* [Argo ワークフロー](https://github.com/argoproj/argo-workflows)
* 青/緑、カナリア、A/Bテストを含む強力なKubernetesネイティブリリースの[Flagger](https://github.com/fluxcd/flagger)
* CDに完全に関連しているわけではありませんが、ボイラープレートを減らし、yaml/jsonマニフェスト間の共有を増やすためのテンプレート言語である[jsonnet](https://jsonnet.org/)をチェックアウトします。