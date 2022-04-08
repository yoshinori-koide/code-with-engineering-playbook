# データ・モデル

## 目次

- [頂点とエッジをグラフ化する](#graph-vertices-and-edges)
- [グラフのプロパティ](#graph-properties)
- [頂点の説明](#vertex-descriptions)
- [フルロールJSONの例](#full-role-json-example)

## グラフモデル

## 頂点とエッジをグラフ化する

グラフモデルの頂点（エンティティ）とエッジ（関係）のセット

| 頂点（出典） | エッジタイプ  | 関係タイプ	 | 頂点（ターゲット） | ノート                                                                                                | 必須 |
| --------------- | ---------------- | ----------------- | --------------- | ---------------------------------------------------------------------------------------------------- | -------- |
| Profession      | _Applies_        | 1:many            | Discipline      | 分類の最上位レベル                                                                     | \*       |
| Discipline      | _Defines_        | 1:many            | Role            | 職業内の関連する役割のグループ                                                          | \*       |
|                 | _AppliedBy_      | 1:1               | Profession      |                                                                                                      | 1        |
| Role            | _Requires_       | 1:many            | 従業員にマッピングされた個々の役割                                                                | 1+       |
|                 | _Requires_       | 1:many            | Competency      |                                                                                                      | 1+       |
|                 | _RequiredBy_     | 1:1               | Discipline      |                                                                                                      | 1        |
|                 | _Succeeds_       | 1:1               | Role            | 役割間のキャリアアップをサポート                                                            | 1        |
|                 | _Precedes_       | 1:1               | Role            | 役割間のキャリアアップをサポート                                                            | 1        |
|                 | _AssignedTo_     | 1:many            | User Profile    |                                                                                                      | \*       |
| Responsibility  | _Expects_        | 1:many            | Key Result      | 役割内の従業員に期待される結果と主要な結果のグループ                             | 1+       |
|                 | _ExpectedBy_     | 1:1               | Role            |                                                                                                      | 1        |
| Competency      | _Describes_      | 1:many            | Behavior        | 成功に貢献する一連の行動                                                       | 1+       |
|                 | _DescribedBy_    | 1:1               | Role            |                                                                                                      | 1        |
| Key Result      | _ExpectedBy_     | 1:1               | Responsibility  | 責任を果たすことの期待される結果                                                  | 1        |
| Behavior        | _ContributesTo_  | 1:1               | Competency      | 自分の行動や行動の仕方                                                       | 1        |
| User Profile    | _Fulfills_       | many:1            | Role            |                                                                                                      | 1+       |
|                 | _Authors_        | 1:many            | Entry           |                                                                                                      | \*       |
|                 | _Reads_          | many:many         | Entry           |                                                                                                      | \*       |
| Entry           | _SharedWith_     | many:many         | User Profile    | ビジネスロジックは、デフォルトでこのリストにマネージャを追加する必要があります。これらのユーザーには、読み取りアクセスのみが必要です。 | \*       |
|                 | _Demonstrates_   | many:many         | Competency      |                                                                                                      | \*       |
|                 | _Demonstrates_   | many:many         | Behavior        |                                                                                                      | \*       |
|                 | _Demonstrates_   | many:many         | Responsibility  |                                                                                                      | \*       |
|                 | _Demonstrates_   | many:many         | Result          |                                                                                                      | \*       |
|                 | _AuthoredBy_     | many:1            | UserProfile     |                                                                                                      | 1+       |
|                 | _DiscussedBy_    | 1:many            | Commentary      |                                                                                                      | \*       |
|                 | _References_     | many:many         | Artifact        |                                                                                                      | \*       |
| Competency      | _DemonstratedBy_ | many:many         | Entry           |                                                                                                      | \*       |
| Behavior        | _DemonstratedBy_ | many:many         | Entry           |                                                                                                      | \*       |
| Responsibility  | _DemonstratedBy_ | many:many         | Entry           |                                                                                                      | \*       |
| Result          | _DemonstratedBy_ | many:many         | Entry           |                                                                                                      | \*       |
| Commentary      | _Discusses_      | many:1            | Entry           |                                                                                                      | \*       |
| Artifact        | _ReferencedBy_   | many:many         | Entry           |                                                                                                      | 1+       |

## グラフのプロパティ

各頂点とエッジで使用可能なデータプロパティのフルセット

| Vertex/Edge    | Property        | Data Type | Notes                                                  | Required |
| -------------- | --------------- | --------- | ------------------------------------------------------ | -------- |
| **(Any)**      | ID              | guid      |                                                        | 1        |
| Profession     | Title           | String    |                                                        | 1        |
|                | Description     | String    |                                                        | 0        |
| Discipline     | Title           | String    |                                                        | 1        |
|                | Description     | String    |                                                        | 0        |
| Role           | Title           | String    |                                                        | 1        |
|                | Description     | String    |                                                        | 0        |
|                | Level Band      | String    | SDE, SDE II, Senior, など                               | 1        |
| Responsibility | Title           | String    |                                                        | 1        |
|                | Description     | String    |                                                        | 0        |
| Competency     | Title           | String    |                                                        | 1        |
|                | Description     | String    |                                                        | 0        |
| Key Result     | Description     | String    |                                                        | 1        |
| Behavior       | Description     | String    |                                                        | 1        |
| User Profile   | Theme selection | string    | 2つだけ : ダーク, ライト　　　　                          | 1        |
|                | PersonaId       | guid[]    | 2つだけ : User, Admin                                   | 1+       |
|                | UserId          | guid      | AADオブジェクトを指す                                   | 1        |
|                | DeploymentRing  | string[]  | 新しいバージョンをデプロイするために使用                  | 1        |
|                | Project         | string[]  | ユーザーが作成したプロジェクトのリスト                    | \*       |
| Entry          | Title           | string    |                                                        | 1        |
|                | DateCreated     | date      |                                                        | 1        |
|                | ReadyToShare    | boolean   | ドラフトの場合はfalse                                   | 1        |
|                | AreaOfImpact    | string[]  | 3つのオプション：自己、他者への貢献、他者の活用            | \*       |
| Commentary     | Data            | string    |                                                        | 1        |
|                | DateCreated     | date      |                                                        | 1        |
| Artifact       | Data            | string    |                                                        | 1        |
|                | DateCreated     | date      |                                                        | 1        |
|                | ArtifactType    | string    | アーティファクトタイプを記述します：マークダウン、blobリンク | 1        |

## 頂点の説明

### Profession

分類の最上位レベル

```json
{
    "title": "Software Engineering",
    "description": "Description of profession",
    "disciplines": []
}
```

### Discipline

職業内の関連する役割のグループ

```json
{
  "title": "Site Reliability Engineering",
  "description": "Description of discipline",
  "roles": []
}
```

### Role

従業員にマッピングされた個々の役割

```json
{
  "title": "Site Reliability Engineering IC2",
  "description": "Detailed description of role",
  "responsibilities": [],
  "competencies": []
}
```

### Responsibility

役割内の従業員に期待される結果と主要な結果のグループ

```json
{
  "title": "Technical Knowledge and Domain Specific Expertise",
  "results": []
}
```

### Competency

成功に貢献する一連の行動

```json
{
  "title": "Adaptability",
  "behaviors": []
}
```

### Key Result

責任を果たすことの期待される結果

```json
{
  "description": "Develops a foundational understanding of distributed systems design..."
}
```

### Behavior

自分の行動や行動の仕方

```json
{
  "description": "Actively seeks information and tests assumptions."
}
```

### User

ユーザーオブジェクトは、人が誰であるかを示します。独自に保存するのではなく、AzureOIDを使用します。

### User Profile

ユーザープロファイルには、メモリに固有のユーザー設定とエッジが含まれています。

### Persona

ユーザーは複数のペルソナを保持できます。

### Entry

同じエントリオブジェクトはさまざまな種類のデータを保持できます。プロジェクトのこの段階では、外部データを保存しないことを決定します。そのため、読者がクリックしてリダイレクトできるように、データへのリンクを提供するのはユーザーの責任です。新しいタブに移動して開きます。

> **Note:** これは、Webアプリで、リンクが新しいタブで開かれていることを確認する必要があることを意味します。

### Project

プロジェクトは、ユーザーがエントリをグループ化する対象を表す単なる文字列フィールドです。

### Area of Impact

これは、HRツールのベン図の3つの影響領域を指します。オプションは次のとおりです。自己、他者の影響に貢献する、他者の仕事に基づいて構築する。

### Commentary

コメントは基本的にテキストの一部です。ただし、エントリを共有する人は誰でも、エントリにコメントを追加できます。

### Artifact

アーティファクトオブジェクトには、関連データがマークダウンとして、または関連データへのリンクとして含まれています。

## フルロールJSONの例

```json
{
  "id": "abc123",
  "title": "Site Reliability Engineering IC2",
  "description": "Detailed description of role",
  "responsibilities": [
    {
      "id": "abc123",
      "title": "Technical Knowledge and Domain Specific Expertise",
      "results": [
        {
          "description": "Develops a foundational understanding of distributed systems design..."
        },
        {
          "description": "Develops an understanding of the code, features, and operations of specific products..."
        }
      ]
    },
    {
      "id": "abc123",
      "title": "Contributions to Development and Design",
      "results": [
        {
          "description": "Develops and tests basic changes to optimize code..."
        },
        {
          "description": "Supports ongoing engagements with product engineering teams..."
        }
      ]
    }
  ],
  "competencies": [
    {
      "id": "abc123",
      "title": "Adaptability",
      "behaviors": [
        { "description": "Actively seeks information and tests assumptions." },
        {
          "description": "Shifts his or her approach in response to the demands of a changing situation."
        }
      ]
    },
    {
      "id": "abc123",
      "title": "Collaboration",
      "behaviors": [
        {
          "description": "Removes barriers by working with others around a shared need or customer benefit."
        },
        {
          "description": " Incorporates diverse perspectives to thoroughly address complex business issues."
        }
      ]
    }
  ]
}
```

## API データモデル

APIコンシューマーから非表示にする必要のある内部エッジまたは頂点がないため、APIは、消費のために現在のデータモデルの1：1マッピングを公開します。データモデルがダウンストリームユーザーにとって複雑になりすぎると、これは変更される可能性があります。
