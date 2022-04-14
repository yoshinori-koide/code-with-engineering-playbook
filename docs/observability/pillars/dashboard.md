# ダッシュボード

## 概要

ダッシュボードは、監視可能なシステムの主要業績評価指標（KPI）の「一目でわかる」ビューを提供するデータ視覚化の形式です。ダッシュボードは複数のデータソースを接続し、他の方法では理解が難しいデータ洞察の視覚的表現を作成できるようにします。ダッシュボードは次の目的で使用できます。

- トレンドを表示
- パターンの特定（ユーザー、使用法、検索など）
- 効率を簡単に測定
- データの外れ値と相関関係を特定する
- システムのヘルス状態またはパフォーマンスを表示する
- ビジネス/プロセスにとって重要なKPIの見通しを示す

## ベストプラクティス

ダッシュボードを作成するときによくある質問は次のとおりです。

- 私のユーザーはほとんどの時間をどこで過ごしましたか？
- 私のユーザーは何を検索していますか？
- アラートとトラブルシューティングでチームをより適切に支援するにはどうすればよいですか？
- 私のシステムは過去1日/週/月/四半期の間正常ですか？

ダッシュボードを作成する際に考慮すべき原則は次のとおりです。

1. 簡単にするために、ダッシュボードを複数のセクションに分けてください。該当する場合は、ページジャンプまたはアンカー（#section）を追加することもプラスです。
2. 複数の単純なグラフを追加します。単純なグラフを作成し、複雑なすべてを1つのグラフにするのではなく、より多くのグラフを作成します。
3. 目標またはKPI測定を特定します。目標またはKPIを特定することは、何を達成する必要があるかを定義するのに役立ちます。ここにいくつかの例があります-サーバーのダウンタイム、エラーに対処するための平均時間、サービスレベルアグリーメント。
4. 定義された目標またはKPIを達成するのに役立つ質問をします。これは直感に反するように聞こえるかもしれませんが、ダッシュボードの作成中に尋ねられる質問が多いほど、結果は良くなります。場所、インターネットサービスプロバイダー、ユーザーがサーバーにリクエストを送信する時刻などの質問は、良いスタートです。
5. 質問を検証します。これは多くの場合、利害関係者、スポンサー、リード、またはプロジェクトマネージャーと一緒に行われます。
6. 構築されたダッシュボードを観察します。データは、利害関係者が答えようとしていることを反映していますか？
7. このプロセスには時間がかかることを常に忘れないでください。ダッシュボードの作成は簡単ですが、パターンを表示するための観察可能なダッシュボードの作成は困難です。

## 推奨ツール

- [Azure Monitor Workbooks](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/workbooks-overview) - マークダウンをサポートする Azure Workbooks は、Azureサービスと緊密に統合されているため、追加のツールを使用せずに高度にカスタマイズできます。
- [ログクエリを使用してダッシュボードを作成する](https://docs.microsoft.com/en-us/azure/azure-monitor/learn/tutorial-logs-dashboards) - ダッシュボードは、Log Analyticsデータのログクエリを使用して作成できます。
- [Application Insightsによるダッシュボードの構築](https://docs.microsoft.com/en-us/azure/azure-monitor/learn/tutorial-app-dashboards) - ダッシュボードは、Application Insightsを使用して作成することもできます。
- [Power Bi](https://docs.microsoft.com/en-us/power-bi/create-reports/service-dashboard-create) - Power Biは、データソースとレポートからダッシュボードを作成するためのより簡単なツールの1つです。
- [Grafana](https://grafana.com/tutorials/) - GGrafanaの使用を開始します。Grafanaは、ダッシュボードと視覚化のための人気のあるオープンソースツールです。
- [GrafanaデータソースとしてのAzure Monitor](https://grafana.com/grafana/plugins/grafana-azure-monitor-datasource) - これにより、AzureMonitorをGrafanaに段階的に統合できます。
- [さまざまなツールの簡単な比較](https://docs.microsoft.com/en-us/azure/azure-monitor/visualizations)

## ダッシュボードのサンプルとレシピ

### Azureワークブック:

- パフォーマンス分析 - システムのパフォーマンスに関する測定。ギャラリーで利用可能なワークブックテンプレート。
- 障害分析 - システム障害に関するレポートと詳細。ギャラリーで利用可能なワークブックテンプレート。
- アプリケーションパフォーマンスインデックス([Appdex](https://en.wikipedia.org/wiki/Apdex)) - これは、ユーザーの満足度を測定する方法です。ベースラインパフォーマンスしきい値Tに基づいて、パフォーマンスを3つのゾーンに分類します。Appdexのテンプレートは、Azureワークブックギャラリーでも利用できます。

### Application Insights:

- [ユーザー維持分析](https://docs.microsoft.com/en-us/azure/azure-monitor/app/usage-retention)
- [ユーザーナビゲーションパターン分析](https://docs.microsoft.com/en-us/azure/azure-monitor/app/usage-flows)
- [ユーザーセッション分析](https://docs.microsoft.com/en-us/azure/azure-monitor/learn/tutorial-users)

他のツールの場合、これらは、テンプレートがすぐに利用できない場合に再作成するための参照として使用できます。

### データソースとしてAzure Monitorを使用するGrafana:

- [Azure Kubernetes サービス - クラスターと名前空間のメトリクス](https://grafana.com/grafana/dashboards/10956) - KubernetesクラスターのContainer Insightsメトリクス。クラスター使用率、名前空間使用率、ノードcpuとメモリ、ノードディスク使用量とディスクio、ノードネットワークとkubeletdocker操作メトリック
- [Azure Kubernetes サービス - コンテナレベルとポッドメトリクス](https://grafana.com/grafana/dashboards/14891) - これには、上記のダッシュボードにないCPUやメモリなどのコンテナレベルとポッドメトリクスが含まれます。

## 概要

監視可能なダッシュボードを構築するための目標は、収集されたメトリック、ログ、トレースを利用して、システムのパフォーマンス、ユーザーの動作、およびパターンの識別に関する洞察を提供することです。そこにはたくさんのツールとテンプレートがあります。どちらを選択しても、優れたダッシュボードは常に、システムとユーザーに関する質問に答え、KPIと目標を追跡し、情報に基づいたビジネス上の意思決定を行うのに役立つダッシュボードです。
