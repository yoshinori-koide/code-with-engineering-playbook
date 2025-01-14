# テスト

※ オリジナル: https://microsoft.github.io/code-with-engineering-playbook/automated-testing/

## テスト手法への結果のマップ

次の表は、結果（検証作業で達成したい結果）を、その結果を達成するために使用できる1つ以上の手法にマップしています。

テーブルを使用するには、目玉を参照するか、キーワードを検索します。

| 私が取り組んでいるとき... | この結果を得たい... | ..だから私は考慮する必要があります |
|--|--|--|
| 開発 | 既存の発信者およびクライアントとの下位互換性を証明する | [シャドウテスト](shadow-testing/README.md) |
| 開発; [統合テスト](integration-testing/README.md) | テレメトリが十分に詳細で完全であることを確認して、 [エンドツーエンドテスト](e2e-testing/README.md) フローの誤動作を追跡および診断します | 分散デバッグの課題; 孤立したコールチェーン分析 |
| 開発 | プログラムロジックが、予想されるさまざまなメインライン、エッジ、および予期しない入力に対して正しいことを確認します | [単体テスト](unit-testing/README.md); 機能テスト; [消費者主導の契約テスト](cdc-testing/README.md); [統合テスト](integration-testing/README.md) |
| 開発 | 論理的正しさの回帰を防ぎます。早いほうがいい | [単体テスト](unit-testing/README.md); 機能テスト; [消費者主導の契約テスト](cdc-testing/README.md); [統合テスト](integration-testing/README.md); リング（これらはそれぞれカバレッジの範囲を拡大しています） |
| 開発 | 機能ポイント（単一のAPIなど）のメインラインの正確性を手動ですばやく検証します | 手動スモークテストツール: postman, powershell, curl |
| 開発 | コンポーネント間の相互作用を個別に検証し、コンシューマーコンポーネントとプロバイダーコンポーネントに互換性があり、契約に文書化されている共通の理解に準拠していることを確認します | [消費者主導の契約テスト](cdc-testing/README.md) |
| 開発; [統合テスト](integration-testing/README.md) | ネットワークホップを含む、コールチェーン内の複数のインターフェイスで複数のコンポーネントが一緒に機能することを検証します | [統合テスト](integration-testing/README.md); [エンドツーエンドテスト](e2e-testing/README.md); セグメント化された [エンドツーエンドテスト](e2e-testing/README.md) |
| 開発	 | 災害時の回復可能性を証明する–データの破損から回復する | DRドリル |
| 開発	 | サービスの認証または承認の脆弱性を見つける | シナリオ（セキュリティ） |
| 開発	 | 正しいRBACを証明し、認証コードの解釈を主張する | シナリオ（セキュリティ） |
| 開発	 | 有効なAPIの使用法を文書化および/または実施する | [単体テスト](unit-testing/README.md); 機能テスト; [消費者主導の契約テスト](cdc-testing/README.md)|
| 開発	 | 依存関係の前に、または依存関係がない場合に、実装の正確さを証明する | [単体テスト](unit-testing/README.md) (モックを使用); [単体テスト](unit-testing/README.md) (エミュレーターを使用); [消費者主導の契約テスト](cdc-testing/README.md) |
| 開発	 | ユーザーインターフェイスにアクセスできることを確認します | アクセシビリティ |
| 開発	 | ユーザーがインターフェースを操作できることを確認します | [UIテスト（自動）](ui-testing/README.md) (人間のユーザビリティ観察) |
| 開発	 | ユーザーエクスペリエンスの低下を防ぐ | UIオートメーション; [エンドツーエンドテスト](e2e-testing/README.md) |
| 開発	 | 「騒々しい隣人」現象を検出して防止する | [負荷テスト](performance-testing/load-testing.md) |
| 開発	 | 可用性の低下を検出する | [合成トランザクションテスト](synthetic-monitoring-tests/README.md); アウトサイドインプローブ |
| 開発	 | 「複合」シナリオのユースケース/ワークフローでのリグレッションを防止します（たとえば、eコマースシステムでは、シーケンスで一緒に使用される多くのAPIが「ショップアンドバイ」シナリオを実行する場合があります） | [エンドツーエンドテスト](e2e-testing/README.md); シナリオ |
| 開発; 運用 | レイテンシー/コスト/リソース消費などの実行時パフォーマンスメトリックのリグレッションを防止します。早いほうがいい | リング; [合成トランザクションテスト](synthetic-monitoring-tests/README.md) / トランザクション; ロールバックウォッチドッグ |
| 開発; 最適化 | 2つの候補実装または機能のバリエーション間で特定のメトリックを比較します | テスト飛行; A/B テスト|
| 開発; ステージング | プロビジョニングされた容量の本番システムが、信頼性、可用性、リソース消費、パフォーマンスの目標を満たしていることを証明する | [負荷テスト (ストレス)](performance-testing/load-testing.md); スパイクテスト; ソークテスト; [性能試験](performance-testing/README.md) |
| 開発; ステージング | 主要なユーザーエクスペリエンスのパフォーマンス特性（遅延、おしゃべり、ネットワークエラーに対する回復力）を理解する | ロード; [性能試験](performance-testing/README.md); シナリオ (ネットワーク分離) |
| 開発; ステージング; 運用 | スタック内の個々のコンポーネントのメルトポイント（障害または最大許容リソース消費が発生する負荷）を検出します | スクイーズ; [負荷テスト (ストレス)](performance-testing/load-testing.md) |
| 開発; ステージング; 運用 | システム全体の融点（エンドツーエンドシステムに障害が発生する負荷）と、スタック全体で最も弱いリンクであるコンポーネントを検出します | スクイーズ; [負荷テスト (ストレス)](performance-testing/load-testing.md) |
| 開発; ステージング; 運用 | 特定のプロビジョニングの容量制限を測定して、将来のプロビジョニングのニーズを予測または満たす | スクイーズ; [負荷テスト (ストレス)](performance-testing/load-testing.md) |
| 開発; ステージング; 運用 | フェイルオーバー手順書の作成/実行 | フェイルオーバードリル |
| 開発; ステージング; 運用 | 災害復旧可能性を証明する–データセンターの喪失（流星シナリオ）。MTTRを測定する | DRドリル |
| 開発; ステージング; 運用 | 可観測性ダッシュボードが正しく、テレメトリが完了しているかどうかを理解します。流れる | トレース検証; [負荷テスト (ストレス)](performance-testing/load-testing.md); シナリオ; [エンドツーエンドテスト](e2e-testing/README.md) |
| 開発; ステージング; 運用 | トラフィックの季節性の影響を測定する | [負荷テスト](performance-testing/load-testing.md) |
| 開発; ステージング; 運用 | トランザクションとアラートを正しく通知/アクションを実行する | [合成トランザクションテスト](synthetic-monitoring-tests/README.md) (ネガティブケース); [負荷テスト](performance-testing/load-testing.md) |
| 開発; ステージング; 運用; 効率化 | スケーラビリティ曲線、つまりシステムが負荷のあるリソースをどのように消費するかを理解する | [負荷テスト (ストレス)](performance-testing/load-testing.md); [性能試験](performance-testing/README.md) |
| 運用; 効率化 | 長距離にわたるシステムの動作を発見する | ソークテスト |
| 効率化 | コスト削減の機会を見つける | スクイーズ |
| ステージング; 運用 | フェイルオーバー/スケールアウト（再パーティション化、プロビジョニングの増加）/スケールダウンの影響を測定する | フェイルオーバードリル; スケールドリル |
| ステージング; 運用 | プロビジョニングを増減するための手順書を作成/実行する | スケールドリル |
| ステージング; 運用 | トラフィックの急激な変化の下での行動を測定する | スパイク |
| ステージング; 効率化 | 単位負荷量あたりのコストメトリックを検出します（どの要素がどの負荷ポイントでのコストに影響するか、たとえば、100万人の同時ユーザーあたりのコスト） | [負荷テスト (ストレス)](performance-testing/load-testing.md)  |
| 開発; 運用 | システムが予測不可能であるが避けられない障害（ネットワークの停止、ハードウェア障害、VMホストのサービス、ラック/スイッチの障害、悪意のある神のランダムな行為、ソーラーフレア、海底ケーブルリレーを食べるサメ、宇宙線、電力）に対して回復力がないポイントを発見します停止、反逆のバックホウオペレーター、ジャンクションボックスを噛むオオカミ、…） | カオステスト |
| 開発 | PowerPlatformカスタムコネクタで単体テストを実行する | [カスタムコネクタテスト](unit-testing/custom-connector.md) |

## テスト内のセクション

- [エンドツーエンドテスト](e2e-testing/README.md)
- [フォールトインジェクションテスト](fault-injection-testing/README.md)
- [統合テスト](integration-testing/README.md)
- [性能試験](performance-testing/README.md)
- [シャドウテスト](shadow-testing/README.md)
- [スモークテスト](smoke-testing/README.md)
- [合成トランザクションテスト](synthetic-monitoring-tests/README.md)
- [UIテスト](ui-testing/README.md)
- [単体テスト](unit-testing/README.md)

## テクノロジー固有のテスト

- [DevTestパターンを使用してAzDOでコンテナーを構築する](tech-specific-samples/azdo-container-dev-test-release)
- [Azuriteを使用してパイプラインでBLOBストレージテストを実行する](tech-specific-samples/blobstorage-unit-tests/README.md)
