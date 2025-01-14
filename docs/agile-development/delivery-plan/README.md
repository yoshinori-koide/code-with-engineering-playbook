# 配送計画

※ オリジナル: https://microsoft.github.io/code-with-engineering-playbook/agile-development/delivery-plan/

## 目標

スクラムは一度に複数のスプリントを計画することを必要とせず、推奨しませんが。私たちのほとんどは、外部のチームに依存している企業で働いています（たとえば、マーケティング、販売、サポート）

計画されたプロジェクト範囲の大まかな評価は、妥当な時間枠とリソース内で達成できます。目標は、「アジャイルフォール」を実装するのではなく、大まかな計画と見積もりを出発点として持つことです。

これは、計画に関する議論を可能にするための出発点にすぎないことに注意してください。実際のスケジュールは時間の経過とともに進化し、シフトすることを期待しており、進行するにつれてスコープとタイムラインを更新することを期待しています。

配信計画により、チームが組織の目標に沿っていることが保証されます。

## 利点

- 評価が完了すると、範囲や時間枠を延期したり、追加のリソースを要求したりできます。
- プロジェクト/製品の提供を進めるにつれ、範囲、時間枠、およびリソースに対するリスクを浮き彫りにすることができます。

## アプローチ

達成するために取ることができる1つのアプローチは、付箋とスプレッドシートを使用することです。

ステップ1：バックログ内のすべての機能をスタックランク付けする

- 機能的特徴
- [非機能的特徴] (docs/TECH-LEADS-CHECKLIST.md)
- ユーザーの調査と設計
- テスト
- ドキュメンテーション
- 知識の伝達/サポートプロセス

ステップ2：1人あたりの労働週数に関するTシャツの機能。シナリオによっては、作業がどれほど複雑かわからない場合があります。この状況では、スパイクを実行する時間を要求できます（タイムボックスを使用して、時間どおりに戻ることができます）。

ステップ3：開始日と終了日を持ち、休日、休暇、会議、トレーニング、およびオンボーディング日を差し引いた週数に基づいて、チームのキャパシティを計算します。また、その人が欠陥やサポートにも取り組んでいる場合はマイナスの時間。

ステップ4：容量に基づいて、オプションがあることがわかります

- より多くのリソースを求めます。注意：新しいリソースのオンボーディングには時間がかかります。
- スコープを最もMVPに減らします。注意：スコープをさらにトリミングすると、顧客にとって価値がなくなる可能性があります。あなたが必要とするすべてであるカップケーキを考えてみてください。あなたはフロスティングをすくい取りたくありません。
- もっと時間を求めてください。通常、これが最も柔軟ですが、ヒットする必要のあるマーケティング日がある場合は、これと同じくらい柔軟な場合があります。

## ツール

エピックと機能を作成し、週の見積もりを追加することで、これらのツールの1つを活用することもできます。

[Azure DevOpsのプラン（プレビュー）機能](https://docs.microsoft.com/en-us/azure/devops/project/navigation/preview-features?view=azure-devops) 機能は、プランの作成に役立ちます。配信計画は、チームが配信する予定のストーリーまたは機能のスケジュールを提供します。配信計画では、カレンダービューに対して、選択したチームのスプリント（反復パス）によってスケジュールされた作業項目が表示されます。

Confluence JIRA、Trello、Rally、Asana、Basecamp、Github Issuesは、市場に出回っている他の同様のツールです（無料のもの、月額料金を支払うもの、オンプレミスでインストールできるもの）を活用できます。
