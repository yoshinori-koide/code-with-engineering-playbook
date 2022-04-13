# コードとしての可観測性

可能な限り、クラウドリソースのプロビジョニング、アラートの監視、ダッシュボードなどの可観測性資産の構成と管理は、コードとして管理する必要があります。コードとしての可観測性は、Terraform / Ansible/ARM テンプレートのいずれかを使用して実現されます

## コードとしての可観測性の例

1. コードとしてのダッシュボード - 監視ダッシュボードは、JSONまたはXMLテンプレートとして作成できます。このテンプレートはソース管理が維持されており、ダッシュボードへの変更を確認できます。ダッシュボードを有効にするための自動化を構築できます。[Azureでこれを行う方法の詳細](https://docs.microsoft.com/en-us/azure/azure-portal/azure-portal-dashboards-create-programmatically)。Grafanaダッシュボードは、自動化とパイプラインで使用するために最終的にソース制御できる[コードとして構成](https://grafana.com/blog/2020/02/26/how-to-configure-grafana-as-code/)することもできます。

2. コードとしてのアラート - アラートは、TerraformまたはARMテンプレートを使用してAzure内で作成できます。このようなアラートは、ソース制御され、パイプライン（Azure DevOpsパイプライン、Jenkins、GitHubアクションなど）の一部としてデプロイできます。これを行う方法のいくつかの参照は次のとおりです。[Terraform Monitor Metric Alert](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/monitor_metric_alert)。アラートは、ログ分析クエリに基づいて作成することもでき、[Terraform Monitorのスケジュールされたクエリルールアラート](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/monitor_scheduled_query_rules_alert#example-usage)を使用してコードとして定義することもできます。

3. ログ分析クエリの自動化 - ログ分析クエリの自動化が必要になる可能性があるいくつかのユースケースがあります。例、自動レポート生成、分析、デバッグなどのためにプログラムでカスタムクエリを実行する。これらのユースケースを機能させるには、ログクエリをソース制御する必要があり、[ログ分析 REST](https://dev.loganalytics.io/documentation/Using-the-API)または[azure cli](https://docs.microsoft.com/en-us/cli/azure/ext/log-analytics/monitor/log-analytics?view=azure-cli-latest)を使用して自動化を構築できます。

## なぜ

- これにより、構成の再現性と自動化が可能になります。また、環境全体でアラートとダッシュボードを最初から手動で構成する必要がなくなります。

- 構成されたダッシュボードは、統合および展開中のエラーのトラブルシューティングに役立ちます（CI / CD）

- 変更を監査し、問題が発生した場合はロールバックできます。

- 本番環境だけでなく、すべての環境で生成されたメトリックデータから実用的な洞察を特定します。

- IACを使用したアラートしきい値、期間、構成値などの可観測性資産の構成と管理は、展開中の構成の間違い、エラー、または見落としを回避するのに役立ちます。

- 可観測性をコードとして実践する場合、他のコードの貢献と同様に、チームが変更を確認できます。
