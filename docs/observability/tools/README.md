# ツールとパターン

システムを監視可能にするための最新のツールがいくつかあります。システムで機能するツールを特定および/または作成する際に、選択をガイドするために考慮すべきいくつかの事項を以下に示します。

- 統合が簡単で使いやすいものでなければなりません。
- データを集約して視覚化できる必要があります。
- ツールはリアルタイムデータを提供する必要があります。
- 適切で適切なエンドツーエンドのコンテキストを使用して、ユーザーを問題のある領域に誘導できる必要があります。

## 選択肢

- [Loki](./loki.md)
- [OpenTelemetry](./OpenTelemetry.md)
- [Kubernetes Dashboards](./KubernetesDashboards.md)
- [Prometheus](./Prometheus.md)

## サービスメッシュ

[サイドカーパターン](https://www.oreilly.com/library/view/designing-distributed-systems/9781491983638/ch02.html#:~:text=The%20sidecar%20pattern%20is%20a,first%20is%20the%20application%20container.&text=In%20addition%20to%20the%20application,without%20the%20application%20container's%20knowledge.)に従うサービスメッシュを利用すると、すぐに使用できるメトリックとトレースのセットが設定されます（ただし、トレースは着信要求から発信要求に手動で伝播する必要があります）。

サイドカーは、画像へのすべての着信および発信トラフィックを傍受することで機能します。次に、各リクエストにトレースヘッダーを追加し、ログとメトリックの標準セットを発行します。これらのメトリックは、可観測性に関して非常に強力であり、クライアント側かサーバー側かに関係なく、すべてのサービスが次のような統一されたメトリックのセットを活用できるようにします。

- レイテンシー
- バイト
- リクエストレート
- エラー率

マイクロサービスアーキテクチャでは、500の急増の根本原因を特定することは簡単ではありませんが、サイドカーからの可観測性が追加されているため、サービスメッシュ内のどのサービスがエラーの急増につながったかをすばやく判断できます。

Service Meshは、構成可能性のために大きな表面積を持っており、展開するのが困難な作業のように見える場合があります。ただし、ほとんどのサービス（Linkerdを含む）は、適切なデフォルトのセットを提供し、これらの可観測性の勝利をすばやく実現するために、ハッピーパスを介して展開できます。
