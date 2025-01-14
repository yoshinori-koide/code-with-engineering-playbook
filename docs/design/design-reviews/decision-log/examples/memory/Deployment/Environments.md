# アプリケーションの展開

メモリアプリケーションは、[Azure DevOps](https://docs.microsoft.com/en-gb/azure/devops/index?view=azure-devops)を活用して、作業項目の追跡、継続的インテグレーション（CI）および継続的デプロイ（CD）を実現します。

---

## 環境

メモリプロジェクトは、複数の環境を使用して、リリースをグローバルユーザーベースにプロモートする前に、変更を分離してテストします。

新しい環境のロールアウトは、前のステージ/環境の展開が成功したことに基づいて自動的にトリガーされます。

**開発**、**ステージング**、および**実稼働環境**は、環境のロールアウト中にスロットの展開を活用します。新しいリリースがステージングスロットにデプロイされた後、一連の機能統合テストを通じて検証されます。すべてのテストの合格率が100％になると、ステージングスロットと本番スロットが効果的に交換され、環境の更新が利用可能になります。

エラーまたは失敗したテストは、現在の段階での展開を停止し、それ以降の環境への変更を防ぎます。

デプロイされた各環境は完全に分離されており、コンポーネントを共有しません。それぞれに、Azure Traffic Manager、CosmosDBなどの一意のリソースインスタンスがあります。

### 展開の依存関係

| 開発環境      　　| ステージング | 本番環境　      |
| ---------------- | ----------- | --------------- |
| CI品質ゲート      | 開発環境　　 | ステージング     |
|                  |             | 手動承認　　　　 |

### ローカル

ローカル環境は、新しい機能やコンポーネントの開発中に個々のソフトウェアエンジニアによって使用されます。

エンジニアは、展開された開発環境の一部のコンポーネントを活用します。これらのコンポーネントは、特定のプラットフォームでは使用できないか、ローカルで実行できません。

- CosmosDB
  - エミュレータはWindowsにのみ存在します

ローカル環境も Azure Traffic Manager を使用しません。フロントエンド Web アプリは、通常は別のローカルホストポートマッピングで実行されているバックエンドREST　APIと直接通信します。

### 開発環境

開発環境は、最初の品質ゲートとして使用されます。`main`ブランチにチェックインされるすべてのコードはすべてのCI品質ゲートが通過した後にこの環境に自動的にデプロイされます。

#### 開発リージョン

- 米国西部（westus）

### ステージング環境

ステージング環境は、本番環境に展開する前に、新機能、コンポーネント、およびその他の変更を検証するために使用されます。この環境は、主に開発者、QA、およびその他の企業の利害関係者によって使用されます。

#### ステージングリージョン

- 米国西部（westus）
- 米国東部（eastus）

### 本番環境

本番環境は、世界中のユーザーベースによって使用されています。この環境への変更は、他の自動品質ゲートに加えて、製品のリーダーシップチームによる手動承認によってゲートされます。

#### 本番環境 リージョン

- 米国西部（westus）
- 米国中部（centralus）
- 米国東部（eastus）

---

## 環境変数グループ

### インフラストラクチャのセットアップ (cse-memory-common)

- appName
- businessUnit
- serviceConnection
- subscriptionId

### 開発セットアップ (cse-memory-dev)

- environmentName (placeholder)

### ステージング設定 (cse-memory-staging)

- environmentName (placeholder)

### 本番セットアップ (cse-memory-prod)

- environmentName (placeholder)
