# プロファイリング

## 概要

プロファイリングは、メモリ割り当て、ガベージコレクション、スレッドとロック、コールスタック、特定の機能の頻度と期間など、ランタイムのさまざまなコンポーネントを測定するランタイム分析の形式です。これを使用して、バイナリで最もコストのかかる関数を確認できるため、最大の非効率性を可能な限り迅速に取り除くことに注力できます。これは、デッドロック、メモリリーク、または非効率的なメモリ割り当てを見つけるのに役立ち、リソース割り当て（つまり、CPUまたはRAM）に関する決定を通知するのに役立ちます。

## アプリケーションのプロファイルを作成する方法

プロファイリングは言語に多少依存するため、「profile $language」を検索することから始めます（いくつかの一般的なツールを以下に示します）。さらに、多くの言語にはC / C ++のバインディングがあるため、LinuxPerfは優れたフォールバックです。

プロファイリングには、コールスタックを検査し、場合によってはアプリケーションをまとめて一時停止する必要があるため、ある程度のコストがかかります（つまり、Javaで完全なGCをトリガーするため）。たとえば、10分ごとに10秒間、サービスのプロファイルを継続的に作成することをお勧めします。これらのパラメータを調整することを決定するときは、コストを考慮してください。

ツールが異なれば、プロファイルの視覚化も異なります。一般的なCPUプロファイルは、有向グラフ ![graph](images/pprof-dot.png)またはフレームグラフを使用する場合があります。 ![flame](images/flame.png)

残念ながら、各プロファイラーツールは通常、プロファイルを保存するために独自の形式を使用し、独自の視覚化が付属しています。

## Specific tools

- (Java, Go, Python, Ruby, eBPF) [Pyroscope](https://github.com/pyroscope-io/pyroscope) 連続プロファイリングをそのまま使用できます。
- (Java and Go) [Flame](https://github.com/VerizonMedia/kubectl-flame) - Kubernetesのコンテナのプロファイリング
- (Java, Python, Go) [Datadog連続プロファイラー](https://www.datadoghq.com/product/code-profiling/)
- (Java, Python, Go, Node.js) [Instana](https://www.instana.com/blog/instana-announces-the-industrys-first-commercial-continuous-production-profiler/)
- (Go) [profefe](https://github.com/profefe/profefe)、継続的なプロファイリングを提供するために `pprof`を構築します
- (Java) [Opsian](https://opsian.com/)
- (Java) [Eclipse Memory Analyzer](https://www.eclipse.org/mat/)
