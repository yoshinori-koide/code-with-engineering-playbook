# レシピ

## Application Insights/ASP.NET

[Github リポジトリ](https://github.com/Azure-Samples/application-insights-aspnet-sample-opentelemetry), [記事](https://devblogs.microsoft.com/aspnet/observability-asp-net-core-apps/).

## Kafkaへの分散トレースコンテキスト伝播を備えた Application Insights/ ASP.NET Core

[Github リポジトリ](https://github.com/MagdaPaj/application-insights-aspnet-sample-trace-context-propagation).

## 例：Terraform を使用してAzure Monitor ダッシュボードとアラートをセットアップする

[Github リポジトリ](https://github.com/buzzfrog/azure-alert-dashboard-terraform)

## オンプレミスのApplication Insights

[オンプレミスのApplication Insights](https://github.com/c-w/appinsights-on-premises)は、Azure App Insightsと互換性のあるサービスですが、データをPostgreSQLなどの社内データベースまたは[Azurite](https://github.com/Azure/Azurite)などのオブジェクトストレージに保存します。

オンプレミスのApplication Insightsは、ソリューションがクラウド展開可能である必要があるが、オンプレミスの切断された展開シナリオもサポートする必要があるシナリオで、Azure Application Insightsのドロップイン代替として役立ちます。

オンプレミスのApplicationInsightsは、テレメトリ統合のテストにも役立ちます。テレメトリに関連する問題は、ライブのAzure Application Insightsリソースをテストに使用するのが簡単ではないため、これらの統合が単体テストまたは統合テストフローから除外されることが多いため、キャッチするのが難しい場合があります。アサーションの古いテレメトリを無視する場合、新しいリソースを使用すると、テレメトリが表示されるまでに時間がかかる場合があります。オンプレミスのApplication Insightsサービスを使用すると、AzureApplicationInsights互換APIとの統合が容易になります。 Azureでリソースを起動することなく、ローカル開発または継続的な統合中のエンドポイント。など。

## Power BI を使用した Azure DevOps パイプラインレポート

[Azure DevOpsパイプラインレポート](https://github.com/Azure-Samples/powerbi-pipeline-report)には、Azure DevOps（AzDO）組織からのプロジェクト、パイプライン、およびパイプライン実行データを監視するための[Power BI](https://docs.microsoft.com/en-us/power-bi/fundamentals/power-bi-overview)テンプレートが含まれています。

このダッシュボードレシピは、さまざまなメトリック（つまり、平均実行時間、実行結果統計など）をテーブルに表示することにより、AzDOパイプラインの可観測性を提供します。さらに、テンプレートの2ページ目は、PowerBIチャートを使用してパイプラインの成功と失敗の傾向を視覚化します。ドキュメントとセットアップ情報は、プロジェクトREADMEにあります。

## OpenCensus を使用したApplication InsightsのPythonLoggerクラス

このリポジトリには、Opencensusを使用するApplication InsightsのPythonロガークラスである「AppLogger」クラスが含まれています。また、「AppLogger」の使用法を示すサンプルコードも含まれています。

[GitHub リポジトリ](https://github.com/Azure-Samples/azure-monitor-opencensus-python/tree/master/azure_monitor/python_logger_opencensus_azure)
