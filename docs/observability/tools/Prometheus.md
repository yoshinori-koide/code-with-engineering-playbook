# Prometheus

## 概要

もともとSoundCloudで構築された[Prometheus](https://prometheus.io/docs/introduction/overview/)は、時系列メトリックデータに基づくオープンソースの監視およびアラートツールキットです。これは、クラウドネイティブの世界でデファクトスタンダードのメトリクスソリューションになり、Kubernetesで広く使用されています。

Prometheusの中核は、メトリックをスクレイプして保存するサーバーです。Prometheusの機能を基本を超えて拡張するためのプログラミング言語用のアラートマネージャや[クライアントライブラリ](https://prometheus.io/docs/instrumenting/clientlibs/)など、他にも多数のオプション機能やコンポーネントがあります。クライアントライブラリは`Counter`、`Gauge`、`Histogram`および`Summary`の4つの[メトリックタイプ](https://prometheus.io/docs/concepts/metric_types/)を提供します。

## なぜ Prometheus?

- Prometheus は時系列データベースであり、イベントまたは測定値を追跡、監視、および時間の経過とともに集計することができます。
- Prometheus はプルベースのツールです。他の監視ツールに対する Prometheus の最大の利点の1つは、Prometheus がターゲットからメトリックを取得するためにターゲットを積極的にスクレイプすることです。Prometheus は、メトリックをプッシュするためのプッシュモデルもサポートしています。
- Prometheusを使用すると、スクレイプする方法と、スクレイプする頻度を制御できます。Prometheusサーバーを介して、複数のスクレイプ構成が可能であり、さまざまなターゲットに対して複数のレートを使用できます。
- [Grafana](https://prometheus.io/docs/visualization/grafana/) と同様に、時系列の視覚化はPrometheus Web UIを介して直接実行できます。Web UIは、さまざまなターゲットで何が起こっているかを簡単にフィルタリングして概要を把握する機能を提供します。
- Prometheusは、PromQL（Prometheus Query Language）と呼ばれる強力な機能クエリ言語を提供します。これにより、ユーザーは時系列データをリアルタイムで集計できます。

## 他のツールとの統合

Prometheusクライアントライブラリを使用すると、コードにインストルメンテーションを追加し、HTTPエンドポイントを介して内部メトリックを公開できます。現在、公式の[Prometheusクライアントライブラリ](https://prometheus.io/docs/instrumenting/clientlibs/)は `Go`、`Java or Scala`、`Python`および`Ruby` です。非公式のサードパーティライブラリには、`.NET/C#`、`Node.js`、および `C++`が含まれます。

Prometheusのメトリック形式は、次のようなさまざまなツールとサービスでサポートされています。

- [Azure Monitor](https://docs.microsoft.com/en-us/azure/azure-monitor/containers/container-insights-prometheus-integration)
- [Stackdriver](https://cloud.google.com/stackdriver/docs/solutions/gke/prometheus)
- [Datadog](https://docs.datadoghq.com/integrations/prometheus/)
- [CloudWatch](https://aws.amazon.com/blogs/containers/using-prometheus-metrics-in-amazon-cloudwatch/)
- [New Relic](https://docs.newrelic.com/docs/integrations/prometheus-integrations/get-started/send-prometheus-metric-data-new-relic/)
- [Flagger](https://docs.flagger.app/tutorials/prometheus-operator)
- [Grafana](https://grafana.com/docs/grafana/latest/getting-started/getting-started-prometheus/)
- [GitLab](https://docs.gitlab.com/ee/user/project/integrations/prometheus.html)
- [その他...](https://prometheus.io/docs/operating/integrations/)

サードパーティのデータベース、ハードウェア、CI / CDツール、メッセージングシステム、API、およびその他の監視システムから既存のメトリックをエクスポートする際に使用される[多数のエクスポーター](https://prometheus.io/docs/instrumenting/exporters/)があります。クライアントライブラリとエクスポーターに加えて、サービスディスカバリ、リモートストレージ、アラート、および管理のための[統合ポイント](https://prometheus.io/docs/operating/integrations/)が多数あります。

## 参考文献

- [Prometheus ドキュメント](https://prometheus.io/docs)
- [Prometheus ベストプラクティス](https://prometheus.io/docs/practices)
- [GrafanaとPrometheus](https://prometheus.io/docs/visualization/grafana/)
