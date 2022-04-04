# テスト駆動開発の例

※ オリジナル: https://microsoft.github.io/code-with-engineering-playbook/automated-testing/unit-testing/tdd_example/

この方法では、すべてのテストを事前に作成するのではなく、一度に1つのテストを作成してから、そのテストに合格するシステムコードを作成するように切り替えます。実際には「正しくない」場合でも、必要最小限のコードを記述することが重要です。テストに合格したら、コードをリファクタリングして意味をなすようにすることができますが、ロジックは単純である必要があります。より多くのテストを作成すると、ロジックはますます複雑になりますが、作成されたすべてのコードがカバーされているため、システムコードに最小限の変更を自信を持って継続できます。

例として、文字列が有効なパスワード形式であることを検証する新しい関数を作成しようとしていると仮定します。パスワードの形式は、少なくとも1つの数字を含む8文字を超える文字列である必要があります。可能な限り単純なテストから始めます。これを行う最も簡単な方法の1つは、関数への入力を検証するテストを最初に作成することです。

```csharp
// Tests.cs
public class Tests
{
    [Fact]
    public void ValidatePassword_NullInput_Throws()
    {
        var s = new MyClass();
        Assert.Throws<ArgumentNullException>(() => s.ValidatePassword(null));
    }
}

// MyClass.cs
public class MyClass
{
    public bool ValidatePassword(string input)
    {
        return false;
    }
}
```

このコードを実行すると、`ValidatePassword`のコードは単なるスタブであるため、例外はスローされずに、テストは失敗します。これで結構です！これは、Red-Green-Refactorの「赤」の部分です。次に、「グリーン」の部分に移ります。このテストに合格するために必要な最小限の変更を加えます。

```csharp
// MyClass.cs
public class MyClass
{
    public bool ValidatePassword(string input)
    {
        throw new ArgumentNullException(nameof(input));
    }
}
```

テストに合格しましたが、この関数は実際には機能しません。常に例外がスローされます。それで大丈夫です！テストを書き続けると、この関数のロジックがゆっくりと追加され、テストに合格し続けることを保証しながら、それ自体に基づいて構築されます。

リファクタリングするものがないため、この時点では「リファクタリング」段階をスキップします。次に、パスワードがサイズ8未満の場合に関数がfalseを返すことを確認するテストを追加しましょう。

```csharp
[Fact]
public void ValidatePassword_SmallSize_ReturnsFalse()
{
    var s = new MyClass();
    Assert.False(s.ValidatePassword("abc"));
}
```

このテストは、`ArgumentNullException`をスローするだけなので合格しますが、これも予想される失敗です。関数を修正すると、次のようになります。

```csharp
public bool ValidatePassword(string input)
{
    if (input == null)
    {
        throw new ArgumentNullException(nameof(input));
    }

    return false;
}
```

最後に、本物に見えるいくつかのコード！nullチェックの`if`ステートメントを追加したのは、nullをチェックしたテストではなく、まったく新しいブランチのロックを解除した後続のテストであったことに注意してください。そのifステートメントを追加することで、**両方**のテストに合格するために必要な最小限の変更を加えましたが、まだやるべきことがあります。

一般に、ポジティブテストを追加する前に、最初にネガティブテストを追加する順序で作業することで、両方のケースがテストを取得できる方法でコードによってカバーされるようになります。赤-緑-リファクタリングは、最小限の変更を要求することで、そのプロセスを非常に簡単にします-最小限の変更のみを行いたいので、ここでは単にfalseを返します。これを拡張するロジックを後で追加することを十分に理解しています。 。

そういえば、今すぐポジティブテストを追加しましょう：

```csharp
[Fact]
public void ValidatePassword_RightSize_ReturnsTrue()
{
    var s = new MyClass();
    Assert.True(s.ValidatePassword("abcdefgh1"));
}
```

繰り返しますが、このテストは開始時に失敗します。ここで注意すべきことの1つは、テストを将来の変更に対して回復力のあるものにすることが重要である場合です。テスト対象のコードを作成するときは、非常に素朴に行動し、合格した現在のテストを作成しようとするだけです。ただし、テストを作成するときは、実行しているすべてが将来的に有効なケースであることを確認する必要があります。この場合、abcdefgh最終的に合格する関数を記述したときに入力文字列を記述できた可能性がありますが、後で関数に残りの適切な入力があることを検証するテストを追加すると、誤って失敗します。

とにかく、次のコード変更は次のとおりです。

```csharp
public bool ValidatePassword(string input)
{
    if (input == null)
    {
        throw new ArgumentNullException(nameof(input));
    }

    if (input.Length > 8)
    {
        return true;
    }
    return false;
}
```

これで合格テストができました！ただし、ロジックは実際にはあまり意味がありません。より長い文字列に合格する新しい条件を追加するという最小限の変更を行いましたが、将来的には、検証を追加するとすぐには機能しないことがわかっています。それでは、赤-緑-リファクタリングフローの最初の「リファクタリング」ステップを使用しましょう！

```csharp
public bool ValidatePassword(string input)
{
    if (input == null)
    {
        throw new ArgumentNullException(nameof(input));
    }

    if (input.Length < 8)
    {
        return false;
    }
    return true;
}
```

それは良く見えます。関数の観点から、ifステートメントを反転しても関数が返すものは変わらないことに注意してください。これはリファクタリングフローの重要な部分であり、通常はIDEのツールと自動リファクタリングを使用して、確実に安全なリファクタリングを実行することでロジックを維持します。


最後に、この`ValidatePassword`メソッドの最後の要件が1つあります。それは、パスワードに番号が含まれていることを確認する必要があるということです。再び否定的なテストから始めて、数値を渡さなかった場合に関数が`false`を返すかどうか有効な長さの文字列でそれを検証しましょう。

```csharp
[Fact]
public void ValidatePassword_ValidLength_ReturnsFalse()
{
    var s = new MyClass();
    Assert.False(s.ValidatePassword("abcdefghij"));
}
```

もちろん、テストは長さの要件をチェックしているだけなので失敗します。数字をチェックする方法を修正しましょう：

```csharp
public bool ValidatePassword(string input)
{
    if (input == null)
    {
        throw new ArgumentNullException(nameof(input));
    }

    if (input.Length < 8)
    {
        return false;
    }

    if (!input.Any(char.IsDigit))
    {
        return false;
    }
    return true;
}
```

ここでは、便利なLINQメソッドを使用して、`string`のいずれかの`char`が数字であるかどうかを確認し、そうでない場合はfalseを返します。テストに合格し、リファクタリングできます。読みやすくするために、`if`ステートメントを組み合わせてみませんか。

```csharp
public bool ValidatePassword(string input)
{
    if (input == null)
    {
        throw new ArgumentNullException(nameof(input));
    }

    if ((input.Length < 8) ||
        (!input.Any(char.IsDigit)))
    {
        return false;
    }

    return true;
}
```

このコードをリファクタリングすると、ポジティブシナリオとネガティブシナリオの両方をテストする100％のテストカバレッジがあるため、行った変更に100％自信があります。この場合、実際にはポジティブケースをテストするメソッドがすでにあるので、関数は完了です。

コードが完全にテストされたので、あらゆる種類の変更を加えても、それが機能することを確信できます。たとえば、正規表現を使用するようにメソッドの実装を変更したい場合でも、すべてのテストは合格し、引き続き有効です。

それだ！関数の記述が完了し、100％のテストカバレッジがあります。もう少し複雑なことを行った場合、テストが最初に記述されて以来、設計したものはすべてテスト可能であることが保証されます。