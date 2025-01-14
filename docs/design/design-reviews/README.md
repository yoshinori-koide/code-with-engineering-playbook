# デザインレビュー

## 目次

- [目標](#目標)
- [対策](#対策)
- [影響](#影響)
- [参加](#参加)
- [ファシリテーションガイダンス](#ファシリテーションガイダンス)
- [テクニカルスパイク](#テクニカルスパイク)

## 目標

- お客様の技術的負債を削減します
- ゲームプランのレビュー後も設計を繰り返します
- マイクロソフトとお客様が参照できる有用な技術成果物を生成する

## 対策

### 変更のコスト

エンジニアリングプロセスの一部として設計レビューを組み込む場合、実装を開始する前に決定が前倒しされます。設計段階でAppServicesの代わりにAzureKubernetesServiceを使用することを決定するには、ドキュメントを更新するだけで済みます。ただし、実装の開始後またはソリューションの使用後にこのピボットを作成すると、はるかにコストがかかります。

これらの変更は、実装の前または後に発生していますか？彼らは通常どれくらいの努力をしますか？

### レビューアの参加

作成されたデザイン全体で何人の個人が参加しますか？累積的に、これがより大きな数である場合、これはアイデアと視点のより広い貢献を示します。数が少ない（つまり、すべてのレビューで同じ2人の個人）場合は、視点のセットが限られていることを示している可能性があります。コア開発チームの外部から参加している人はいますか？

### 潜在的な解決策への時間

要件からソリューションオプション（複数）に移行するのに通常どのくらい時間がかかりますか？

さまざまな潜在的なソリューションの評価に費やす時間が多すぎるか少なすぎるかの間には、健全なバランスを取る行為があります。時間が少なすぎると、実装後にコストのかかる変更が必要になるリスクが高くなります。時間が長すぎると、ターゲット値の配信が遅れます。キュー内の後続の機能と同様に。ただし、チームが十分な情報に基づいた意思決定を行うために必要な最も重要な情報を迅速に特定できるほど、コストのかかる変更のリスクを抑えながら、より迅速な価値を提供できます。

### 決定までの時間

どのソリューションを実装するかを決定するのにどのくらい時間がかかりますか？

チームの配信を妨げずに、健全な討論をサポートするための健全なバランスを取る行為もあります。理想的なケースは、チームが提示されたソリューションオプションをすばやく消化し、質問をして、特定のアプローチで最終的に定足数に達する前に議論することです。クォーラムに到達できない場合は、問題について最もコンテキストのある人（通常はストーリーの所有者）が最終決定を下す必要があります。価値の提供と学習を優先します。同意しないでコミットしてください！

## 影響

- ソリューションは、お客様の本番環境に迅速に運用できます
- 他の開発クルーがチームの作業を活用しやすく
- エンジニアがプロジェクトを立ち上げるのが簡単
- コストが最も低いときに変更と決定をフロントローディングすることにより、チームの速度を上げます
- 幅広いレビューアの参加を求めることにより、チームの関与と透明性が向上します

## 参加

### 開発クルー

開発クルーは常にすべての設計レビューセッションに参加する必要があります

- [CSE](../../CSE.md) エンジニアリング
- カスタマーエンジニアリング

### ドメインエキスパート

ドメインの専門家は、必要に応じて設計レビューセッションに参加する必要があります

- CSE 技術ドメイン
- 顧客の対象分野の専門家（SME）
- シニアリーダーシップ

## ファシリテーションガイダンス

### レシピ

設計プロセスのガイダンスについては、[設計レビューのレシピ](./recipes/README.md)を参照してください。

### 対面/仮想会議を介して設計レビューを同期する

開発クルー、対象分野の専門家（SME）、カスタマーエンジニアとの合同会議

### プルリクエストによる非同期設計レビュー

非同期設計レビューを容易にするためのガイダンスについては、[非同期設計レビューのレシピ](./recipes/async-design-reviews.md)を参照してください。これは、異なるタイムゾーンに地理的に分散しているチームに役立ちます。

## テクニカルスパイク

技術的なスパイクは、新しいテクノロジーが現在の実装に与える影響を評価するために最もよく使用されます。詳しくは[こちら](./recipes/technical-spike.md)をご覧ください。

## 設計ドキュメント

- プロジェクト設計ドキュメントでアーキテクチャ設計を文書化して更新します
- 設計決定を追跡し、[決定ログ](decision-log/README.md)に記録します。
- 与えられた問題に対して複数の解決策が存在する場合の[貿易研究](trade-studies/README.md)における決定プロセスを文書化する
