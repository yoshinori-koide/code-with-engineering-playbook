# プライバシー関連のフレームワーク

以下のツール/フレームワークは、個人データに対してデータ分析またはモデル開発を行う必要がある場合に活用できます。このようなフレームワークを使用するには、プライバシー規制などに準拠するソリューションが必要であり、追加の保護手段を適用する必要があることに注意してください。

## プライバシーフレームワークを活用するための典型的なシナリオ

1. データ主体のプライバシーを保護しながらデータまたは結果を共有する

2. プライベートデータの分析または統計モデリングの実行

3. プライバシーを保護するMLモデルとデータパイプラインの開発

## プライバシーフレームワーク

プライベートデータの保護には、取得から保存、処理、分析、モデリング、レポートや機械学習モデルでの使用まで、データのライフサイクル全体が含まれます。これらの各フェーズでは、適切な保護手段と制限を適用する必要があります。

このセクションでは、プライバシーの保護と保護に活用できるプライバシーフレームワークの **非網羅的なリスト** を提供します。

データライフサイクルの4つの主なユースケースに焦点を当てます。

1. [機密性の低いデータの取得](#obtaining-non-sensitive-data)
2. [信頼できる研究およびモデリング環境の確立](#trusted-research-and-modeling-environments)
3. [プライバシー保護データとMLパイプラインの作成](#privacy-preserving-data-pipelines-and-ml)
4. [データ損失防止](#data-loss-prevention)

### 機密性の低いデータの取得

多くのシナリオでは、アナリスト、研究者、およびデータサイエンティストは、機密性の低いバージョンまたはプライベートデータのサンプルにアクセスする必要があります。このセクションでは、機密性の低いデータを取得するための2つのアプローチに焦点を当てます。

**注：** これら2つのアプローチは、結果に個人データが含まれないことを保証するものではなく、追加の対策を適用する必要があります。

#### データの匿名化

匿名化は、個人データの意図しない開示のリスクを低減するために、データセットに一連の変換を適用するプロセスです。匿名化には、追加の外部情報を使用した再識別に使用できる直接識別子（名前、社会保障番号など）または準識別子の両方の削除または置換が含まれます。

匿名化は、構造化データ、画像、テキストなど、さまざまな種類のデータに適用できます。ただし、非構造化データの匿名化には、多くの場合、検出されないPII（個人識別情報）または非個人情報が編集または置換される可能性のある統計的アプローチが含まれます。

ここでは、オープンソースとして利用可能ないくつかの匿名化ソリューションの概要を説明します。

| ソリューション | ノート |
| ----- | ----- |
| [Presidio](https://microsoft.github.io/presidio) | Presidioは、機密データが適切に管理および管理されるようにするのに役立ちます。クレジットカード番号、名前、場所、社会保障番号、ビットコインウォレット、米国の電話番号、財務データなどの非構造化テキストや画像のテキストで、個人エンティティの高速識別および匿名化モジュールを提供します。カスタムPIIエンティティや言語を検出する場合など、高度なカスタマイズ性が必要な場合に役立ちます。[リポジトリへのリンク](https://aka.ms/presidio)、[ドキュメントへのリンク](https://microsoft.github.io/presidio)、[デモへのリンク](https://aka.ms/presidio-demo)。|
| [匿名化のためのFHIRツール](https://github.com/microsoft/FHIR-Tools-for-Anonymization) | FHIR Tools for Anonymizationは、オンプレミスまたはクラウドで、研究、公衆衛生などの二次利用のために、ヘルスケアFHIRデータ（FHIR = Fast Healthcare Interoperability Resources、Electric Health Recordsを交換するための標準）の匿名化を支援するオープンソースプロジェクトです。追加情報の[リンク](https://github.com/microsoft/FHIR-Tools-for-Anonymization)。FHIR形式（Stu3およびR4）で動作し、匿名化のさまざまな戦略を可能にします（日付シフト、暗号化ハッシュ、暗号化、置換、摂動、一般化）|
| [ARX](https://arx.deidentifier.org/) | 統計モデル、具体的にはk-匿名性、ℓ-多様性、t-近似性、およびδ-存在を使用した匿名化。集約されたデータの匿名化を検証するのに役立ちます。リンク：[レポ](https://github.com/arx-deidentifier/arx)、[ウェブ](https://arx.deidentifier.org/)。Java 製。 |
| [k-Anonymity](https://github.com/Nuclearstar/K-Anonymity) |  k-匿名データセットを生成する方法の例を含むGithubリポジトリ。k-匿名性は、属性を少なくともk人のグループにプールすることにより、個人のプライバシーを保護します。[レポ](https://github.com/Nuclearstar/K-Anonymity/blob/master/k-Anonymity.ipynb) |

#### 合成データの生成

合成データセットは、実際のデータから生成されたデータのリポジトリであり、実際のデータと同じ統計プロパティを持っています。合成データセットが実際のデータの正確なプロキシである程度は、効用の尺度です。このような合成データセットの潜在的な利点は、高品質のラベル付きデータセットを手に入れることがしばしば禁止されている医療分類や財務モデリングなどの機密性の高いアプリケーションにあります。

合成データを作成するための最良の方法を決定するときは、最初に、どのタイプの合成データを使用するかを検討することが不可欠です。選択できるカテゴリは大きく2つあり、それぞれに長所と短所があります。

- 完全合成：このデータには元のデータが含まれていません。つまり、単一ユニットの再識別はほとんど不可能であり、すべての変数は引き続き完全に利用可能です。

- 部分的に合成：機密データのみが合成データに置き換えられます。これには、代入モデルへの大きな依存が必要です。これにより、モデルの依存度が低下しますが、データセット内の実際の値により、ある程度の開示が可能であることを意味します。

| ソリューション | Notes |
| ----- | ----- |
|[Synthea](https://synthetichealth.github.io/synthea/)|Syntheaは、米国国勢調査局の人口統計、米国疾病予防管理センターの有病率と発生率、国立衛生研究所のレポートなど、インターネット上で収集された多数のデータソースを使用して開発されました。ソースコードと疾患モデルには、すべてのデータ、統計、および治療の注釈と引用が含まれています。これらの病気と治療のモデルは、健康記録と適切に相互作用します。|
|[PII dataset generator](https://github.com/microsoft/presidio-research/blob/master/presidio_evaluator/data_generator/README.md)|偽の名前ジェネレーターの上に開発された合成データジェネレーター。テンプレートを含むテキストファイル（たとえば、私の名前はPERSON）を取得し、プレースホルダーの代わりに偽のPIIエンティティを含む入力サンプルのリストを作成します。|
|[CheckList](https://github.com/marcotcr/checklist) |チェックリストは、NLPモデルの特定の動作機能を体系的に評価するための摂動手法のフレームワークを提供します|
|[Mimesis](https://github.com/lk-geimfari/mimesis )|Mimesisは、さまざまな言語でさまざまな目的のデータを提供するPython用の高性能の偽のデータジェネレーターです。|
|[Faker](https://github.com/joke2k/faker )|Fakerは、偽のデータを生成するPythonパッケージです。データベースをブートストラップする必要がある場合でも、見栄えの良いXMLドキュメントを作成する必要がある場合でも、永続性を入力してストレステストを行う必要がある場合でも、本番サービスから取得したデータを匿名化する必要がある場合でも、Fakerは最適です。|
|[Plaitpy](https://github.com/plaitpy/plaitpy )|plait.pyの背後にある考え方は、興味深い形の偽のデータを簡単にモデル化できるようにすることです。現在、多くの偽のデータジェネレータは、データをIID変数のコレクションとしてモデル化しています。plait.pyを使用すると、これらの変数をつなぎ合わせて、より一貫性のあるモデルにすることができます。|

### 信頼できる研究およびモデリング環境

#### 信頼できる研究環境

信頼できる研究環境（TRE）により、組織は機密データへのアクセスを必要とするアナリスト、データサイエンティスト、および研究者のための安全なワークスペースを作成できます。

TREは、個別のワークスペースの周囲に安全な境界を適用して、情報ガバナンスの制御を可能にします。各ワークスペースには、許可されたユーザーのセットがアクセスでき、機密データの漏洩を防ぎ、データプラットフォームによって提供される1つ以上のデータセットにアクセスできます。

信頼できる研究環境のいくつかの選択肢を強調します。

| ソリューション | Notes |
| ----- |  ----- |
| Azureの信頼できる研究環境  | [リンク](https://github.com/microsoft/azuretre)。Azure用のオープンソースTRE。 |
| Aridhia DRE | [リンク](https://appsource.microsoft.com/en-us/product/web-apps/aridhiainformatics.analytixagility_workspace_123?tab=Overview)

#### 目が離せない機械学習

特定の状況では、データサイエンティストは、表示が許可されていないデータについてモデルをトレーニングする必要がある場合があります。このような場合、「目が離せない」アプローチが推奨されます。目が離せないアプローチは、データサイエンティストに、データに対してスクリプトを実行できる環境を提供しますが、サンプルへの直接アクセスは許可されていません。Azure MLを使用する場合、 [IDベースのデータアクセス](https://docs.microsoft.com/en-us/azure/machine-learning/how-to-identity-based-data-access)などのツールを使用すると、ユーザーに適切な役割を割り当てるとともに、このシナリオを有効にできます。

アイオフ環境内での処理中は、特定の出力（ログなど）のみをユーザーに抽出して戻すことができます。たとえば、ユーザーはモデルをトレーニングしてモデルのパフォーマンスを検査するスクリプトを送信することはできますが、モデルが間違った出力を予測したサンプルを確認することはできません。

アイオフ環境に加えて、このアプローチでは通常、モデル設計の目的で、代表的なクレンジングされたデータのサンプルセットである「アイオン」データセットへのアクセスを提供する必要があります。Eyes-onデータセットは、多くの場合、プライベートデータセットの匿名化されたサブセット、またはプライベートデータセットの特性に基づいて生成された合成データセットです。

#### プライベートデータ共有プラットフォーム

さまざまなツールとシステムにより、さまざまなパーティがプライベートエンティティを保護しながらサードパーティとデータを共有し、データの漏洩の可能性を減らしながらデータを安全に処理できます。これらのツールには、[セキュアマルチパーティ計算（SMPC）](https://en.wikipedia.org/wiki/Secure_multi-party_computation) システム、[準同型暗号化](#準同型暗号化)システム、[機密コンピューティング](https://azure.microsoft.com/en-us/solutions/confidential-compute/)、 [PySift](https://github.com/OpenMined/PySyft)などのプライベートデータ分析フレームワークが含まれます。

### プライバシー保護データパイプラインとML

データが安全な場合でも、データが消費されたときにプライベートエンティティを抽出できます。プライバシー保護データパイプラインとMLモデルは、データクエリまたはモデル予測中のプライベートデータの漏えいのリスクを最小限に抑えることに重点を置いています。

#### 差分プライバシー

差分プライバシー（DP）は、人々のサブグループに関するデータセットから意味のある洞察を抽出できるようにすると同時に、特定の個人のプライバシーを保護することに関して強力な保証を提供するシステムです。これは通常、すべての個人の情報に小さな統計的ノイズを追加し、それによってデータに不確実性を導入することによって達成されます。ただし、収集された洞察は、全体としての人口について学習しようとしていることを正確に表しています。このアプローチは、補助情報を持っている攻撃者による再識別攻撃やデータ再構築に対して堅牢であることが知られています。より包括的な概要については、[差分プライバシー：技術者以外の対象者向けの入門書]((https://dash.harvard.edu/bitstream/handle/1/38323292/4_Wood_Final.pdf?sequence=1&isAllowed=y)をご覧ください。

DPは、国勢調査データからの学習、ユーザーテレメトリデータ分析、広告へのオーディエンスエンゲージメント、PII保護が最も重要な健康データの洞察など、さまざまなシナリオで広く採用されています。ただし、DPは小さなデータセットにはあまり適していません。

DPを実装するツールには、[SmartNoise](https://github.com/opendifferentialprivacy/smartnoise-samples)、[TensorflowPrivacy](https://github.com/tensorflow/privacy)などがあります。

#### 準同型暗号化

準同型暗号化（HE）は暗号化の形式であり、暗号化されたデータを最初に復号化せずに計算を実行できます。計算Fの結果は暗号化された形式であり、暗号化されていない生データに対して計算Fが実行された場合、復号化すると同じ結果が得られます。（[ソース](https://en.wikipedia.org/wiki/Homomorphic_encryption#:~:text=Homomorphic%20encryption%20is%20a%20form,performed%20on%20the%20unencrypted%20data.)）

準同型暗号化フレームワーク：

| ソリューション | Notes |
| ----- | ----- |
| [Microsoft SEAL](https://www.microsoft.com/en-us/research/project/microsoft-seal) | 安全なクラウドストレージと計算、MLモデリング。BFVおよびCKKSスキームをサポートするMicrosoftの広く使用されているオープンソースライブラリ。|
| [Palisade](https://palisade-crypto.org/) | BGV、BFV、CKKS、TFHE、FHEWなどの複数の準同型暗号化スキームをサポートし、マルチパーティをサポートする、DARPAが資金提供する防衛請負業者のコンソーシアムから広く使用されているオープンソースライブラリ。[リポジトリへのリンク](https://gitlab.com/palisade/palisade-release) |
| [PySift](https://github.com/OpenMined/PySyft) | プライベートディープラーニング。PySyftは、PyTorchやTensorFlowなどの主要なディープラーニングフレームワーク内で、連合学習、差分プライバシー、および暗号化計算（Multi-Party Computation（MPC）やHomomorphic Encryption（HE）など）を使用して、モデルトレーニングからプライベートデータを分離します。|

追加のOSSツールのリストは[ここ](https://homomorphicencryption.org/introduction/)にあります。

#### 連合学習

連合学習は、実際のデータを共有することなく、分散型の方法でMLモデルのトレーニングを可能にする機械学習手法です。モデルの処理エンジンにデータを送信する代わりに、モデルをさまざまなデータ所有者に配布し、分散してトレーニングを実行するというアプローチがあります。

連合学習フレームワーク：

| ソリューション | Notes |
| ----- | ----- |
| [TensorFlow Federated Learning](https://github.com/tensorflow/federated) | TensorFlow上に構築されたOSS連合学習システム |
| [FATE](https://fate.fedai.org/) | 展開のためのさまざまなオプションと連合学習に適合したさまざまなアルゴリズムを備えたOSS連合学習システム |
| [IBM Federated Learning](https://github.com/IBM/federated-learning-lib) | エンタープライズ環境に焦点を当てたPythonベースの連合学習フレームワーク。 |

### データ損失防止

組織は、財務データ、専有データ、クレジットカード番号、健康記録、社会保障番号などの機密情報を管理しています。この機密データを保護し、リスクを軽減するために、ユーザーがデータを持ってはいけない人々と不適切に共有することを防ぐ方法が必要です。この方法は、データ損失防止（DLP）と呼ばれます。（[出典](https://docs.microsoft.com/en-us/microsoft-365/compliance/dlp-learn-about-dlp)）

以下では、DLPの2つの側面に焦点を当てます。機密データの分類とアクセス管理です。

#### 機密データの分類

機密データの分類は、組織が機密データとプライベートデータを追跡、監視、保護、および識別することを可能にするため、DLPの重要な側面です。さらに、さまざまな感度レベルをさまざまなデータアイテムに適用できるため、適切なガバナンスとカタログ化が容易になります。

通常、4つのレベルのデータ分類レベルがあります。

1. Public
2. Internal
3. Confidential
4. Restricted / Highly confidential

Azureでのデータ分類のためのツール：

| ソリューション | Notes |
| ----- | ----- |
| [Microsoft Information Protection](https://docs.microsoft.com/en-us/microsoft-365/compliance/information-protection) (MIP) | DLP、機密データの分類、カタログ化などのためのスイート。|
| [Azure Purview](https://azure.microsoft.com/en-us/services/purview/) | 機密データの分類とカタログ化を含む、統合されたデータガバナンスサービス。Azure Purviewは、データ分類などにMIPテクノロジを活用します。|
| [Azure SQLデータベース、Azure SQLマネージドインスタンス、およびAzureSynapseのデータ検出と分類](https://docs.microsoft.com/en-us/azure/azure-sql/database/data-discovery-and-classification-overview) | Azure SQLおよびSynapseデータベースの機密データを検出、分類、ラベル付け、およびレポートするための基本機能。 |
| [SQLServerのデータ検出と分類](https://docs.microsoft.com/en-us/sql/relational-databases/security/sql-data-discovery-and-classification?view=sql-server-ver15&tabs=t-sql) | SQL Serverデータベース内の機密データを検出、分類、ラベル付け、およびレポートする機能。 |

多くの場合、匿名化に使用されるツールは、機密データの分類子としても機能します。このようなツールについては、[匿名化ツール](#データの匿名化)を参照してください。

追加のリソース：

- [データ分類のガイドライン例](https://www.cmu.edu/iso/governance/guidelines/data-classification.html)
- [感度レベルについて学ぶ](https://docs.microsoft.com/en-us/microsoft-365/compliance/sensitivity-labels?view=o365-worldwide)

#### アクセス管理

アクセス制御は、設計上プライバシーの重要なコンポーネントであり、全体的なデータライフサイクル保護に分類されます。アクセス制御が成功すると、データへのアクセス権を持つ必要がある許可された個人にのみアクセスが制限されます。環境内でデータが保護されたら、誰がどのような目的でこのデータにアクセスする必要があるかを確認することが重要です。 アクセス制御は、サブスクリプション内のリソースで実行される操作への洞察を提供できる[アクティビティログ](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/platform-logs-overview)の統合を含む包括的なログ戦略で監査 できます。

- [OWASPアクセス制御に関するチートシート](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Access_Control_Cheat_Sheet.md)
