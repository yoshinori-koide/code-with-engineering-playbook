# Kubernetes UI ダッシュボード

このドキュメントでは、Kubernetesクラスタでアプリケーションを監視およびデバッグするための便利なツールであるさまざまなKubernetesUIダッシュボードのオプションと利点について説明します。これにより、これらのダッシュボードを介して、クラスターで実行されているアプリケーションの管理、デバッグ、およびクラスターの管理が可能になります。

## 概要と背景

すべてのソリューションをローカルで実行できるとは限らない場合があります。この制限は、環境をローカルでデバッグするための堅牢で効率的な方法を提供しないクラウドサービスが原因である可能性があります。このような場合、Kubernetesでアプリケーションを監視する機能を提供する他のツールを使用する必要があります。

## 利点とユースケース

- Kubernetesクラスターの運用面を表示、管理、監視する機能を提供します。

- UIダッシュボードを使用する利点は次のとおりです。
  - クラスターの概要を見る
  - アプリケーションをクラスターにデプロイする
  - クラスタで実行されているアプリケーションのトラブルシューティング
  - Kubernetesリソースの表示、作成、変更、削除
  - Kubernetesオブジェクトのリソース使用量を含む基本的なリソースメトリックを表示する
  - ログの表示とアクセス
  - ポッドの状態のライブビュー（例：開始、終了など）

- ダッシュボードが異なれば機能も異なり、特定のダッシュボードを選択するユースケースは要件によって異なります。たとえば、多くのダッシュボードは、Kubernetes上のアプリケーションのみを監視する方法を提供しますが、それらを管理する方法は提供しません。

## オープンソースダッシュボード

現在、アプリケーションを監視したり、Kubernetesで管理したりするために利用できるUIダッシュボードがいくつかあります。例えば：

- [Octant](https://github.com/vmware-tanzu/octant)
- [PrometheusとGrafana](https://prometheus.io/docs/visualization/grafana/)
  - [Kube Prometheus スタックチャート](https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack): PrometheusOperatorを使用してPrometheusでエンドツーエンドのKubernetesクラスターモニタリングを操作する簡単な方法を提供します。
- [K8Dash](https://github.com/indeedeng/k8dash)
- [kube-ops-view](https://github.com/hjacobs/kube-ops-view): ノードの占有率と使用率を視覚化するツール
- [Lens](https://k8slens.dev/): クライアント側デスクトップツール
- [Thanos](https://github.com/thanos-io/thanos)および[Cortex](https://cortexmetrics.io/docs/): マルチクラスターの実装

## 参考文献

- [Kubernetesダッシュボードの代替](https://octopus.com/blog/alternative-kubernetes-dashboards)
- [Kubernetesを用いたPrometheusとGrafana](https://tanzu.vmware.com/developer/guides/kubernetes/observability-prometheus-grafana-p1/)
