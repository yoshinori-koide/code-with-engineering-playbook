# プロジェクトとリポジトリ

すべてのソースコードリポジトリには、それに固有のドキュメント（たとえば、リポジトリ内のWiki）が含まれている必要がありますが、プロジェクト自体には、関連するすべてのリポジトリに共通の一般的なドキュメント（たとえば、バックログ管理ツール内のWiki）が含まれている必要があります。 ）。

## リポジトリに固有のドキュメント

- 序章
- 入門
  - オンボーディング
  - セットアップ：プログラミング言語、フレームワーク、プラットフォーム、ツールなど。
  - サンドボックス環境
  - 労働協約
  - 寄稿ガイド
- 構造：フォルダ、プロジェクトなど。
- ソリューション/各プロジェクトをコンパイル、テスト、ビルド、デプロイする方法
  - 異なるOSバージョン
  - コマンドライン+エディター/IDE
- [設計決定ログ](../../design/design-reviews/decision-log/README.md)
  - [アーキテクチャ決定記録（ADR）](../../design/design-reviews/decision-log/README.md#architecture-decision-record-ADR)
  - [貿易研究](../../design/design-reviews/trade-studies/README.md)

リポジトリのドキュメントの一部のセクションは、プロジェクトのドキュメントを指している場合があります（たとえば、オンボーディング、作業契約、寄稿ガイド）。

## すべてのリポジトリに共通のドキュメント

- 序章
  - 計画
  - 利害関係者
  - 定義
  - 要件
- [オンボーディング](../../developer-experience/recipes/onboarding-guide-template.md)
- リポジトリガイド
  - 生産、スパイク
- [チーム契約](../../agile-development/team-agreements/README.md)
  - [チームマニフェスト](../../agile-development/team-agreements/team-manifesto.md)
    - チームでの技術的な働き方とサポートされている考え方に関する期待の簡単な要約。
    - たとえば、所有権、尊重、コラボレーション、透明性。
  - [労働協約](../../agile-development/team-agreements/working-agreements.md)
    - 私たちがチームとしてどのように協力するか、そして私たちの期待と原則は何ですか。
    - たとえば、コミュニケーション、ワークライフバランス、スクラムリズム、バックログ管理、コード管理。
  - [完了の定義](../../agile-development/team-agreements/definition-of-done.md)
    - ユーザーストーリー、スプリント、またはマイルストーンを閉じるために完了する必要があるタスクのリスト。
  - [レディの定義](../../agile-development/team-agreements/definition-of-ready.md)
    - スプリント計画の見積もりの​​候補として選択されるためには、ユーザーストーリーがどの程度完全である必要がありますか。
- 寄稿ガイド
  - リポジトリ構造
  - 設計図書
  - [ブランチおよびブランチ命名戦略](../../source-control/naming-branches.md)
  - [履歴戦略をマージしてコミットする](../../source-control/merge-strategies.md)
  - [プルリクエスト](./pull-requests.md)
  - [コードレビュープロセス](../../code-reviews/README.md)
  - [コードレビューチェックリスト](../../code-reviews/process-guidance/reviewer-guidance.md)
    - [言語固有のチェックリスト](../../code-reviews/recipes/README.md)
- [プロジェクトデザイン](../../design/design-reviews/README.md)
  - [ハイレベル/ゲームプラン](../../design/design-reviews/recipes/high-level-design-recipe.md)
  - [マイルストーン/エピックデザインレビュー](../../design/design-reviews/recipes/milestone-epic-design-review-recipe.md)
- [デザインレビューテンプレート](../../design/design-reviews/README.md#design-review-templates)
  - [マイルストーン/エピックデザインレビューテンプレート](../../design/design-reviews/recipes/milestone-epic-design-review-template.md)
  - [特集/ストーリーデザインレビューテンプレート](../../design/design-reviews/recipes/feature-story-design-review-template.md)
  - [タスクデザインレビューテンプレート](../../design/design-reviews/recipes/task-design-review-template.md)
  - [決定ログテンプレート](../../design/design-reviews/decision-log/doc/decision-log.md)
  - [アーキテクチャ決定レコード（ADR）テンプレート](../../design/design-reviews/decision-log/README.md#architecture-decision-record-ADR) ([例1](../../design/design-reviews/decision-log/doc/adr/0001-record-architecture-decisions.md), [例2](../../design/design-reviews/decision-log/doc/adr/0002-app-level-logging.md))
  - [貿易調査テンプレート](../../design/design-reviews/trade-studies/template.md)
