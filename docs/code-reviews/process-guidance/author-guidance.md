# 著者ガイダンス

※ オリジナル: https://microsoft.github.io/code-with-engineering-playbook/code-reviews/process-guidance/author-guidance/

## プルリクエスト（PR）を適切に説明してください

- PRにわかりやすいタイトルを付けて、他のメンバーがPRの内容を簡単に（1つの短い文で）理解できるようにします。
- すべてのPRには、レビュー担当者に変更された内容とその理由を示す適切な説明が必要です。

## 関連するレビューアを追加する

- 1人以上のレビューア（プロジェクトのガイドラインに応じて）をPRに追加します。理想的には、少なくとも専門知識があり、プロジェクトに精通している人、または使用されている言語を追加します
- プロジェクトや言語にあまり詳しくない人を追加すると、変更が理解可能で読みやすいことを確認し、チーム内の専門知識を高めるのに役立ちます。
- CSEコード-顧客チームとのプロジェクトでは、知識の伝達のために両方の組織からのレビュー担当者を含めることが重要です - [レビュー担当者ポリシーをカスタマイズします](../tools.md#reviewer-policies)

## フィードバックを受け取るためにオープンになります

設計/コードロジックについて話し合い、すべてのコメントに次のように対処します。

- 要求された変更が行われた場合は、コメントを解決します。
- 要求された変更を行わず、明確な理由を提供する予定がない場合は、コメントを「修正しない」としてマークします
  - 要求された変更がタスクの範囲内にある場合、「後で実行します」は許容できる理由ではありません。
  - 要求された変更が範囲外の場合は、その変更用の新しい作業項目（タスクまたはバグ）を作成します
- コメントがわからない場合は、プライベートチャットではなく、レビュー自体で質問してください。
- 結論なしにスレッドが肥大化した場合は、レビュー担当者とミーティングを行います（電話するか、ドアをノックします）

## チェックリストを使用する

PRを作成するときは、説明にPRの目的のチェックリストを追加することをお勧めします。これは、レビュー担当者がコード変更の重要な領域に集中するのに役立ちます。

## タスクをPRにリンクする

対応する作業項目/タスクをPRにリンクします。作業項目とPRの間で情報を複製する必要はありませんが、どちらか一方に詳細が欠落している場合は、それらが一緒になってレビュー担当者により多くのコンテキストを提供します。

## レビューの前にコードに注釈を付ける必要があります

大規模なPRを回避できない場合は、レビュー担当者がコードをレビューしやすくするために変更の説明を含め、レビュー担当者がすべてのコードブロックの目標を特定できる明確なコメントを付けます。
