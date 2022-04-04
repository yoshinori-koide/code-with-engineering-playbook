# 例：単体テストの作成

※ オリジナル: https://microsoft.github.io/code-with-engineering-playbook/automated-testing/unit-testing/authoring_example/

オブジェクト指向言語のいくつかの単体テスト手法を説明するために、単体テストを追加したいコードの例から始めましょう。この例では、作成しているアプリのすべてのスタートアップオプションを含む構成クラスがあります。通常は.configファイルから読み取りますが、現在の実装には3つの問題があります。

1. Configurationクラスにバグがあり、構成ファイルの読み取りに依存しているため、単体テストはありません。
2. 構成ファイルを読み取るConfigurationクラスに依存するコードを単体テストすることはできません。
3. 将来的には、構成をクラウドに保存し、RESTAPIを介してアクセスできるようにする必要があります。

修正しようとしているバグは、構成ファイルに複数の空の行がある場合、IndexOutOfRangeExceptionがスローされることです。現在、クラスは次のようになっています。:

```csharp
using System.IO;
using System.Linq;

public class Configuration
{
    // Public getter properties from configuration object
    public string MyProperty { get; private set; }

    public void Initialize()
    {
        var configContents = File.ReadAllLines(".config");

        // Config is in the format: key=value
        var config = configContents.Select(l => l.Split('='))
                                   .ToDictionary(kv => kv[0], kv => kv[1]);

        // Assign all properties here
        this.MyProperty = config["myproperty"];
    }
}
```

## 抽象化

この例では、ファイルシステムという1つの依存関係があります。ファイルシステムを完全に抽象化するのではなく、ファイルシステムが必要な理由を考えて、実装ではなく概念を抽象化してみましょう。この場合、`File`クラスを使用して構成ファイルと構成の内容を読み取ります。ここでの抽象化の概念は、文字列配列で構成の各行を返すフォームまたは構成リーダーです。これをと呼ぶことができ 、内容を返す`ConfigurationReader`単一のメソッド、`Read`があります。

抽象化を作成するときは、それをサポートする言語で、その抽象化のインターフェースを作成することをお勧めします。C＃の例では、`IConfigurationReader`インターフェイスを作成できます。`ConfigurationReader`クラスを作成するだけでなく 、ファイルシステムから読み取ることを示す場合は、より具体的に`FileConfigurationReader`と名前を付けることができます。

```csharp
// IConfigurationReader.cs
public interface IConfigurationReader
{
    string[] Read();
}

// FileConfigurationReader.cs
public class FileConfigurationReader : IConfigurationReader
{
    public string[] Read()
    {
        return File.ReadAllLines(".config");
    }
}
```

ファイルの依存関係が抽象化されたので、`File.ReadAllLines`メソッドを直接呼び出すのではなく、新しい抽象化を使用するようにConfigurationクラスのInitializeメソッドを更新する必要があります。

```csharp
public void Initialize()
{
    var configContents = new FileConfigurationReader().Read();

    // Config is in the format: key=value
    var config = configContents.Select(l => l.Split('='))
                               .ToDictionary(kv => kv[0], kv => kv[1]);

    // Assign all properties here
    this.MyProperty = config["myproperty"];
}
```

ご覧のとおり、ファイルシステムへの依存関係はまだありますが、その依存関係は抽象化されています。依存関係を完全に解消するには、他の手法を使用する必要があります。

## 依存性注入

前のセクションでは、ファイルアクセスをに抽象化しました`FileConfigurationReader`が、関数内のファイルシステムに依存していました。依存性注入を使用して、適切なリーダーを`Configuration` クラスに注入できます。

```csharp
using System.IO;
using System.Linq;

public class Configuration
{
    private readonly IConfigurationReader configReader;

    // Public getter properties from configuration object
    public string MyProperty { get; private set; }

    public Configuration(IConfigurationReader reader)
    {
        this.configReader = reader;
    }

    public void Initialize()
    {
        var configContents = configReader.Read();

        // Config is in the format: key=value
        var config = configContents.Select(l => l.Split('='))
                                   .ToDictionary(kv => kv[0], kv => kv[1]);

        // Assign all properties here
        this.MyProperty = config["myproperty"];
    }
}
```

上記では、[コンストラクタインジェクション](https://en.wikipedia.org/wiki/Dependency_injection#Constructor_injection)と呼ばれる手法が使用されました。これは、オブジェクトのコンストラクターを使用して依存関係を設定します。つまり、オブジェクトを作成する `Configuration`オブジェクトは、どのリーダーを渡す必要があるかを制御します。これは、以前は`Configuration`オブジェクトが依存関係を制御していた「制御の反転」の例です。このオブジェクトを作成するコンポーネントにコントロールをプッシュしました。

具象クラスではなく、`IConfigurationReader`インターフェースを挿入したことに注意してください。これにより、依存関係を解消できます。元々は`File`クラスがハードコードされた依存関係がありましたが、現在は`IConfigurationReader`を実装するオブジェクトのみに依存しています。

## 最初の単体テストを書く

Configurationユニットテストがないためにキャッチされなかった`Configuration`クラスのバグがあるため、このベンチャーを開始しました。バグによって記述されたシナリオをテストするテストを含む、`Configuration`クラスの完全なカバレッジを提供するいくつかの単体テストを作成しましょう（構成ファイルに複数の空の行がある場合、IndexOutOfRangeExceptionがスローされます）。

ただし、まだ1つの問題があり、`IConfigurationReader`の実装は1つだけであり、ファイルシステムを使用します。つまり、作成する単体テストはファイルシステムに依存します。幸い、依存性注入を使用したので、ファイルシステムに依存しない実装を作成するだけです。ここでモックを作成することもできますが、代わりに、渡されたstring[] を返すだけの`IConfigurationReader`インターフェイスの具体的な実装を作成しましょう。そしてこれを`PassThroughConfigurationReader`と呼びましょう。（このアプローチがモックよりも優れている理由の詳細については、[モック](mocking.md)のページを参照してください）。）。

```csharp
public class PassThroughConfigurationReader : IConfigurationReader
{
    private readonly string[] contents;

    public PassThroughConfigurationReader(string[] contents)
    {
        this.contents = contents;
    }

    public string[] Read()
    {
        return this.contents;
    }
}
```

この単純なクラスは単体テストで使用されるため、多くのファイルアクセスを必要とせずにさまざまな状態を作成できます。これで、現在の動作を説明するテストから始めて、単体テストを作成できます。

```csharp
public class ConfigurationTests
{
    [Fact]
    public void Initialize_EmptyConfig_Throws()
    {
        var reader = new PassThroughConfigurationReader(Array.Empty<string>());
        var config = new Configuration(reader);

        Assert.Throws<KeyNotFoundException>(() => config.Initialize());
    }

    [Fact]
    public void Initialize_CorrectFormat_SetsProperty()
    {
        var reader = new PassThroughConfigurationReader(new[] {
            "myproperty=myvalue"
        });
        var config = new Configuration(reader);

        config.Initialize();

        Assert.Equal("myvalue", config.MyProperty);
    }
}
```

## バグの修正

現在のすべてのテストに合格し、100％のカバレッジが得られますが、バグから明らかなように、考えられるすべての入力と出力をカバーしてはなりません。バグの場合、複数の空の行が問題を引き起こします。さらに、 `KeyNotFoundException`は非常にわかりやすい例外ではなく、実装の詳細であり、構成APIを設計するときに意味のあるものではありません。さらにいくつかのテストを追加し、`Configuration`クラスがどのように動作するかをテストを調整してみましょう 。

```csharp
public class ConfigurationTests
{
    [Fact]
    public void Initialize_EmptyConfig_Throws()
    {
        var reader = new PassThroughConfigurationReader(Array.Empty<string>());
        var config = new Configuration(reader);

        Assert.Throws<InvalidOperationException>(() => config.Initialize());
    }

    [Fact]
    public void Initialize_MalformedLine_Throws()
    {
        var reader = new PassThroughConfigurationReader(new[] {
            "myproperty",
        });
        var config = new Configuration(reader);

        Assert.Throws<InvalidOperationException>(() => config.Initialize());
    }

    [Fact]
    public void Initialize_MultipleEqualSigns_PropertyContainsNoEquals()
    {
        var reader = new PassThroughConfigurationReader(new[] {
            "myproperty=myval1=myval2",
        });
        var config = new Configuration(reader);

        config.Initialize();

        Assert.Equal("myval1=myval2", config.MyProperty);
    }

    [Fact]
    public void Initialize_WithBlankLines_Ignores()
    {
        var reader = new PassThroughConfigurationReader(new[] {
            "myproperty=myvalue",
            string.Empty,
        });
        var config = new Configuration(reader);

        config.Initialize();

        Assert.Equal("myvalue", config.MyProperty);
    }

    [Fact]
    public void Initialize_CorrectFormat_SetsProperty()
    {
        var reader = new PassThroughConfigurationReader(new[] {
            "myproperty=myvalue"
        });
        var config = new Configuration(reader);

        config.Initialize();

        Assert.Equal("myvalue", config.MyProperty);
    }
}
```

これで、4つの失敗したテストと1つの合格したテストがありますが、これらのテストを使用して、呼び出し元がConfigurationクラスを使用する方法と、入力として許可されるものと許可されないものをしっかりと確立しました。次に、テストに合格するように`Configuration`クラスを修正する必要があります 。

```csharp
public void Initialize()
{
    var configContents = configReader.Read();

    if (configContents.Length == 0)
    {
        throw new InvalidOperationException("Empty config");
    }

    // Config is in the format: key=value
    var config = configContents.Where(l => !string.IsNullOrWhiteSpace(l))
                               .Select(l =>
                               {
                                   var splitLine = l.Split('=', 2);
                                   if (splitLine.Length < 2)
                                   {
                                       throw new InvalidOperationException("Malformed line");
                                   }
                                   return splitLine;
                               })
                               .ToDictionary(kv => kv[0], kv => kv[1]);

    // Assign all properties here
    this.MyProperty = config["myproperty"];
}
```

これで、すべてのテストに合格しました。バグを修正し、`Configuration`クラスに単体テストを追加し、将来の変更に対する信頼度を大幅に高めました。

## テスト不可能なコード

[抽象化](README.md#abstraction)のセクションで説明したように、すべてのコードを適切に単体テストできるわけではありません。この例では、テストカバレッジが0％の単一のクラスである `FileConfigurationReader` があります。
これは予想されることです。今回の例では、`FileConfigurationReader`をサードパーティの依存関係を呼び出す以外の追加のロジックを使用せずに、可能な限り軽量に保ちました。`FileConfigurationReader`は[ファサードデザインパターン](https://en.wikipedia.org/wiki/Facade_pattern)の例です。

## テスト可能な設計と将来の改善

この例で説明されている元々の問題の1つは、将来、WebAPIから構成をロードすることを期待していることです。構成テキストをロードする方法を抽象化し、ファイルシステムへの依存関係を解消するすべての作業を行うことで、この将来のシナリオを可能にするためのすべてのハードワークをすでに実行しました。次に行う必要があるのは、`WebApiConfigurationReader`実装を作成し、それを使用して`Configuration`オブジェクトを構築することだけです。これで問題なく動作するはずです。

これは、テスト可能な設計の利点の1つであり、テストを安全な方法で作成する過程で、その副作用として、抽象化されて変更される可能性のある依存関係がすでに存在し、実装に最小限の変更が必要になることがあります。

もう1つの追加の利点は、このテスト可能な設計によって開かれる複数の可能性があることです。たとえば、テスト専用に作成したものを含め、3つの`IConfigurationReader`実装すべてを使用してカスケード構成をセットアップできます。まず、インターネットアクセスが利用可能かどうかを確認し、利用できる場合は`WebApiConfigurationReader`を使用します。インターネットが利用できない場合は、`FileConfigurationReader`を使用して現在のシステムのローカル構成ファイルにフォールバックできます。何らかの理由で構成ファイルが存在しない場合は、コードのどこかにハードコードされたデフォルト構成として`PassThroughConfigurationReader`を使用できます。私たちは将来必要になるかもしれないことを何でもするための完全な柔軟性を持っています！
