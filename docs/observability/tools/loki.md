# Loki

Lokiは、Prometheusからの学習に触発された、Grafana Labsによって作成された、水平方向にスケーラブルで可用性の高いマルチテナントログ集約システムです。ロキは一般に「プロメテウスですが、ログ用」と呼ばれ、これは完全に理にかなっています。どちらのツールも同じアーキテクチャに従います。これは、ソフトウェアシステムの各コンポーネントでメトリックを収集するエージェント、ログを保存するサーバー、およびlokiサーバーにアクセスして視覚化とクエリを構築するGrafanaダッシュボードです。そうは言っても、Lokiには3つの主要なコンポーネントがあります。

## Promtail

Lokiのエージェント部分です。たとえば、var /log/などの複数の場所からログを取得するために使用できます。Promtailの構成は、```config-promtail.yml```と呼ばれるyamlファイルです。このファイルには、Lokiサーバーで集約されるすべてのパスとログソースが記述されています。

## Loki サーバー

Loki サーバーは、すべての異なるシステムから受信したすべてのログを受信して​​保存する責任があります。Lokiサーバーは、たとえばGrafanaで実行されるクエリも担当します。

## Grafanaダッシュボード

Grafanaダッシュボードは、視覚化の作成とクエリの実行を担当します。結局のところ、これは、適切なアクセス権を持つユーザーがログインして、集約されたログのアラートを表示、クエリ、および作成できるWebページになります。

## Lokiを使用する理由

他のログ集計ツールの代わりにLokiを使用する主な理由は、Lokiが必要なストレージを最適化することです。これは、プロメテウスと同じパターンに従うことで実現されます。プロメテウスは、ラベルにインデックスを付け、ログ自体のチャンクを作成し、生のログを保存するよりも少ないスペースを使用します。

## 参考文献

- [Loki 公式サイト](https://grafana.com/oss/loki/)
- [Lokiへのログの挿入](https://grafana.com/docs/loki/latest/getting-started/get-logs-into-loki/)
- [LokiSourceをGrafanaに追加](https://grafana.com/docs/grafana/latest/datasources/loki/#adding-the-data-source)
- [Lokiのベストプラクティス](https://grafana.com/docs/loki/latest/best-practices/)
