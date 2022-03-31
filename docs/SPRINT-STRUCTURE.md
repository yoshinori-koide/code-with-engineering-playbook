# スプリントの構造

※ オリジナル: https://microsoft.github.io/code-with-engineering-playbook/SPRINT-STRUCTURE/

このドキュメントの目的は次のとおりです。:

- クイックリファレンスと発見可能性のためにプレイブックのコンテンツを整理する
- エンジニアリングプロセスを反映する論理構造でコンテンツを提供する
- チームが対象分野の深い専門知識を共有できるようにする拡張可能な階層

## CSEプロジェクトの最初の週

### プロジェクトを開始する前に

- [ ] [チーム契約について話し合い、書き始め](agile-development/team-agreements/README.md)ます。 プロジェクト全体で行われたプロセスの決定でこれらのドキュメントを更新します
  - [労働協約](agile-development/team-agreements/working-agreements.md)
  - [レディの定義](agile-development/team-agreements/definition-of-ready.md)
  - [完了の定義](agile-development/team-agreements/definition-of-done.md)
  - [推定](agile-development/sprint-planning/estimation.md)
- [ ] [リポジトリ/リポジトリを設定する](source-control/README.md#creating-a-new-repository)
  - リポジトリ構造を決定する
  - [README.md](resources/templates/README.md), [LICENSE](resources/templates/LICENSE), [CONTRIBUTING.md](resources/templates/CONTRIBUTING.md)、.gitignoreなどを追加します
- [ ] [製品バックログを作成する](agile-development/backlog-management/README.md)
  - 選択したプロジェクト管理ツール（Azure DevOpsなど）でプロジェクトをセットアップします
  - ユーザーストーリーと受け入れ基準に[INVEST](<https://en.wikipedia.org/wiki/INVEST_(mnemonic)>)する。
  - [非機能要件ガイダンス](design/design-patterns/non-functional-requirements-capture-guide.md)

### 1日目

- [ ] [最初のスプリントを計画する](agile-development/sprint-planning/README.md)
  - スプリントの目標と、スプリントの進捗状況を測定する方法について合意する
  - チームの能力を決定する
  - ユーザーストーリーをスプリントに割り当て、ユーザーストーリーをタスクに分割します
  - 仕掛品（WIP）制限の設定
- [ ] [テストフレームワークを決定し、テスト戦略について話し合う](automated-testing/README.md)
  - テストの目的と目標、およびテストカバレッジの測定方法について話し合う
  - ユニットテストを統合、負荷、およびスモークテストから分離する方法について合意する
  - 最初のテストケースを設計する
- [ ] [ブランチの命名を決定する](source-control/naming-branches.md)
- [ ] [セキュリティのニーズについて話し合い、秘密がソース管理されていないことを確認します](continuous-delivery/secrets-management/recipes/azure-devops/secrets-per-branch.md)

### 2日目

- [ ] [ソース管理を設定する](source-control/README.md)
  - [コミットのベストプラクティスについて](source-control/README.md#commit-best-practices)合意する
- [ ] [リンターと自動テストを使用した基本的な継続的インテグレーションを設定します](continuous-integration/README.md)
- [ ] [毎日のスタンドアップミーティングを設定し、プロセスリードを決定します](agile-development/stand-ups/README.md)
  - 目的、目標、参加者、促進ガイダンスについて話し合う
  - タイミングと、効率的なスタンドアップを実行する方法について話し合います
- [ ] [プロジェクトにサブチームがある場合は、スクラムのスクラムを設定します](agile-development/scrum-of-scrums.md)

### 3日目

- [ ] [コードスタイル](code-reviews/README.md) と [プルリクエストの割り当て方法について合意する](code-reviews/pull-requests.md)
- [ ] [プルリクエスト（2人のレビュー担当者、リンター、自動テスト）のビルド検証を設定](code-reviews/README.md)し、[完了の定義](agile-development/team-agreements/definition-of-done.md)に同意する。
- [ ] [コードマージ戦略に同意](source-control/merge-strategies.md)し、[CONTRIBUTING.md](resources/templates/CONTRIBUTING.md)を更新します
- [ ] [ロギングと可観測性のフレームワークと戦略について合意する](observability/README.md)

### 4日目

- [ ] [継続的展開を設定する](continuous-delivery/README.md)
  - このソリューションに適した環境を決定する
  - 環境ごとに目的について話し合い、展開をトリガーするタイミング、展開前の承認者、昇進のための歌唱。
- [ ] [バージョン管理戦略を決定する](source-control/component-versioning.md)
- [ ] [機能を設計し、設計レビューを実施する](design/design-reviews/README.md)方法について合意する

### 5日目

- [ ] スプリントデモを実施する
- [ ] [ふりかえりを行う](agile-development/retrospectives.md)
  - 必要な参加者、入力（ツール）および結果をキャプチャする方法を決定します
  - タイムラインを設定し、ファシリテーション、会議の構成などについて話し合います。
- [ ] [バックログを調整する](agile-development/backlog-management/backlog-refinement.md)
  - 必要な参加者を決定する
  - [Readyの定義](agile-development/team-agreements/definition-of-ready.md)を更新します
  - 見積もりと、[見積もり](agile-development/sprint-planning/estimation.md) ドキュメントを更新する。
- [ ] [発生した問題についてエンジニアリングフィードバックを送信する](engineering-feedback/README.md)
