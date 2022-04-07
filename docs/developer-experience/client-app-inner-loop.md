# 問題文

クライアントアプリは通常、リモートサービスに依存してアプリを強化します。ただし、クライアントアプリとサービス間の開発スケジュールは必ずしも完全に一致しているわけではありません。高速内部開発ループの場合、クライアントアプリの開発をバックエンドサービスから切り離し、アプリがローカルテスト用のサービスを「呼び出す」ことを許可する必要があります。

## オプション

クライアントアプリの開発をバックエンドサービスから切り離すためのいくつかのオプションがあります。オプションは、サービスのモック実装をアプリケーションに埋め込むことから、サービスの簡略化されたバージョンに依存するものまでさまざまです。

このドキュメントでは、いくつかのオプションをリストし、トレードオフについて説明します。

## 埋め込まれたモック

組み込みの模擬ソリューションには、サービスインターフェイスをローカルに実装するクラスが含まれています。モデルまたはデータ転送オブジェクトまたはDTOとも呼ばれるインターフェイスとデータクラスは、nswag（[RicoSuter / NSwag：.NET、ASP.NET Core、TypeScript用のSwagger / OpenAPIツールチェーン（github.com）](https://github.com/RicoSuter/NSwag)）またはautorest（[Azure / autorest：OpenAPI（または Swagger）仕様コードジェネレーター。C＃、PowerShell、Go、Java、Node.js、TypeScript、Python、Ruby（github.com）をサポート](https://github.com/Azure/AutoRest)）などのツールを使用して、サービスのAPI仕様から生成されることがよくあります。 

単純なサービス実装は静的応答を返すことができます。RESTfulサービスの場合、スタブのJSON応答は、アプリケーションリソースとして、または単に静的文字列として保存できます。

```C#
public Task\<UserProfile> GetUserAsync(long userId, CancellationToken
cancellationToken)
{
    PetProfile result =
Newtonsoft.Json.JsonConvert.DeserializeObject\<UserProfile>(

            MockUserProfile.UserProfile,

                new Newtonsoft.Json.JsonSerializerSettings());

    return Task.FromResult(result);
}
```

より洗練されたものは、ランダムにエラーを返し、アプリの復元力コードパスをテストできます。

モックは、条件付きコンパイルを介して、またはアプリ構成を介して動的にアクティブ化できます。いずれの場合も、混乱を招き、潜在的な脆弱性が含まれることを避けるために、モック、サービスレスポンス、および外部化された構成が最終リリースに含まれないようにすることをお勧めします。

### サンプル：依存性注入によるモックの登録

Unity（[UnityContainerの紹介|Unity Container](http://unitycontainer.org/articles/introduction.html) ）のような依存性注入コンテナーを使用すると、モックサービスと実際のサービスクライアントの実装を簡単に切り替えることができます。どちらも同じインターフェースを実装しているため、Unityコンテナーに実装を登録できます。

```C#
public static void Bootstrap(IUnityContainer container)
{

#if DEBUG
    container.RegisterSingleton\<IUserServiceClient, MockUserService>();
#else
    container.RegisterSingleton\<IUserServiceClient, UserServiceClient>();
#endif

}
```

インターフェイスを消費するコードは、違いに気付かないでしょう。

```C#
public class UserPageModel

{

    private readonly IUserServiceClient userServiceClient;

    public UserPageModel(IUserServiceClient userServiceClient)
    {
        this.userServiceClient = userServiceClient;

    }

// ...
}
```

## インターフェイスを消費するコードは、違いに気付かないでしょう。

ローカルで実行されるサービスを使用するアプローチは、クライアントでの呼び出しを、実際のエンドポイント（dev、QA、prodなど）を指すことからローカルエンドポイントに置き換えることです。

このアプローチでは、Postman（ [Postman APIプラットフォーム|無料でサインアップ](https://www.postman.com/)）やFiddler（[Fiddler | Webデバッグプロキシおよびトラブルシューティングソリューション（telerik.com）](https://www.telerik.com/fiddler) ）などのトラフィックキャプチャおよびシェーピングプロキシを挿入することもできます。

このアプローチの利点は、APIがクライアントから切り離されており、クライアントに変更を加えることなく、独立して更新/変更（応答コードの変更、データの変更など）できることです。これは、新しい開発シナリオのロックを解除するのに役立ち、開発フェーズ中に柔軟性を提供します。

このアプローチの課題は、サービスのセットアップ、構成、およびローカルでの実行が必要になることです。そのプロセスを簡素化するのに役立つツールがあります（例： [JsonServer](https://www.npmjs.com/package/json-server)、[Postman Mock Server](https://learning.postman.com/docs/designing-and-developing-your-api/mocking-data/setting-up-mock/)）。

### 忠実度の高いローカルサービス

ローカルサービススタブは、期待されるAPIを実装します。埋め込みモックと同様に、既存のAPIコントラクト（OpenAPIなど）に基づいて生成できます。

忠実度の高いアプローチでは、クライアントアプリがローカルのデバッグとテストのために開始される前に、docker-composeを使用してローカルで実行できるdockerコンテナーに、実際のサービスと簡略化されたデータをパッケージ化します。サービスを完全にローカルで実行できるようにするには、「ローカルバージョン」は、依存するクラウドサービスをローカルの代替手段に置き換えます。たとえば、blobの代わりにファイルストレージを使用し、SQLAzureDBの代わりにSQLServerをローカルで実行します。

このアプローチにより、分散展開をスピンアップすることなく、完全な忠実度の統合テストも可能になります。

## スタブ/フェイクサービス

忠実度の低いアプローチでは、API仕様から生成できるスタブサービスを実行するか、JsonServer（[JsonServer.io：プロトタイピングとテスト用の偽のjsonサーバーAPIサービス]((https://www.jsonserver.io/)）やPostmanなどの偽のサーバーを実行します。これらのサービスはすべて、事前に決定され構成されたJSONメッセージで応答します。

偽のサービスを使用して実装する方法の例は、[ここにあり](./recipes/fake-services-inner-loop.md)ます。

## 決定方法

|| 長所| 短所| 開発時の例： | 使用しない場合の例  |
|------------|-------------|-------------|---------------|------------|
| 埋め込まれたモック | F5開発者エクスペリエンスを簡素化します |クライアントと緊密に結合| より静的なタイプのデータシナリオ  | テスト（単体テスト、統合テストなど）  |
|| 管理する外部依存関係はありません | ハードコードされたデータ | サービスとの最初の統合 |
||| 依存性注入によるモックは、些細なことではありません。 ||
| 忠実度の高いローカルサービス | クライアントから緩く結合 | 追加のツールが必要、つまりローカルインフラストラクチャのオーバーヘッド | URLルート  | API契約が利用できない場合   |
||応答を個別に変更するのが簡単| サービスの追加のセットアップと構成 |
||	サービスの独立した更新||
||HTTPトラフィックを利用できます|||
||後で実際のサービスに簡単に置き換えることができます |||
|スタブ/フェイクサービス|クライアントから緩く結合|追加のツールが必要、つまりローカルインフラストラクチャのオーバーヘッド| 応答コード | API契約が利用可能な場合 |
||応答を個別に変更するのが簡単|サービスの追加のセットアップと構成|複雑/可変データシナリオ| API契約が利用可能な場合 |
||	サービスの独立した更新|期待されるAPIの完全な忠実度を提供しない可能性があります ||
||HTTPトラフィックを利用できます|||
||	後で実際のサービスを利用するのが簡単 |||
