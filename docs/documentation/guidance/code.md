# コード

**自己文書化コードを書くべきだ**と何度も聞いたことがあるかもしれません。これは、コードにコメントを付けてはいけないという意味ではありません。

コードコメントには、実装コメントとドキュメントコメントの2種類があります。

## 実装コメント

これらは内部ドキュメントに使用され、将来の自分自身を含め、将来コードを保守する必要がある可能性のあるすべての人を対象としています。

1行コメントと複数行コメント（[C＃コメント](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/language-specification/lexical-structure#comments)など）があります。コメントは人間が読める形式で実行されないため、コンパイラによって無視されます。したがって、必要な数だけ追加できる可能性があります。

現在、これらのコメントの使用は、コードの臭いと見なされることがよくあります。コードを明確にする必要がある場合は、コードが複雑すぎる可能性があります。したがって、コードをより単純にし、読みやすく、理解しやすくすることで、説明の削除に向けて取り組む必要があります。それでも、これらのコメントは、コードの概要を示したり、コード自体では利用できない追加のコンテキスト情報を提供したりするのに役立ちます。

有用なコメントの例：

- そのコードが存在する**理由を**説明するC＃の1行コメント（[System.Text.Json.JsonSerializer](https://github.com/dotnet/runtime/blob/main/src/libraries/System.Text.Json/src/System/Text/Json/Serialization/JsonSerializer.Read.String.cs)のプライベートメソッドから）：

```csharp
// For performance, avoid obtaining actual byte count unless memory usage is higher than the threshold.
Span<byte> utf8 = json.Length <= (ArrayPoolMaxSizeBeforeUsingNormalAlloc / JsonConstants.MaxExpansionFactorWhileTranscoding) ? ...
```

- **追加のコンテキスト**を提供するC＃の複数行コメント（[System.Text.Json.Utf8JsonReader](https://github.com/dotnet/runtime/blob/main/src/libraries/System.Text.Json/src/System/Text/Json/Reader/Utf8JsonReader.cs)のプライベートメソッドから）：

```csharp
// Transcoding from UTF-16 to UTF-8 will change the length by somewhere between 1x and 3x.
// Un-escaping the token value will at most shrink its length by 6x.
// There is no point incurring the transcoding/un-escaping/comparing cost if:
// - The token value is smaller than charTextLength
// - The token value needs to be transcoded AND unescaped and it is more than 6x larger than charTextLength
//      - For an ASCII UTF-16 characters, transcoding = 1x, escaping = 6x => 6x factor
//      - For non-ASCII UTF-16 characters within the BMP, transcoding = 2-3x, but they are represented as a single escaped hex value, \uXXXX => 6x factor
//      - For non-ASCII UTF-16 characters outside of the BMP, transcoding = 4x, but the surrogate pair (2 characters) are represented by 16 bytes \uXXXX\uXXXX => 6x factor
// - The token value needs to be transcoded, but NOT escaped and it is more than 3x larger than charTextLength
//      - For an ASCII UTF-16 characters, transcoding = 1x,
//      - For non-ASCII UTF-16 characters within the BMP, transcoding = 2-3x,
//      - For non-ASCII UTF-16 characters outside of the BMP, transcoding = 2x, (surrogate pairs - 2 characters transcode to 4 UTF-8 bytes)

if (sourceLength < charTextLength
    || sourceLength / (_stringHasEscaping ? JsonConstants.MaxExpansionFactorWhileEscaping : JsonConstants.MaxExpansionFactorWhileTranscoding) > charTextLength)
{
```

## ドキュメントのコメント

ドキュメントコメントは特別な種類のコメントであり、ユーザー定義のタイプまたはメンバーの定義の上に追加され、独自のコードでそれらのタイプまたはメンバーを使用する必要がある可能性のあるすべての人を対象としています。

たとえば、ライブラリまたはフレームワークを構築している場合は、ドキュメントコメントを使用してドキュメントを生成できます。このドキュメントは、API仕様やプログラミングガイドとして機能する必要があります。

Docコメントは、単一行および複数行のコメントと同様に、コンパイラによって最終的な実行可能ファイルに含まれません。

C＃でのドキュメントコメントの例（[System.Text.Json.JsonSerializer](https://github.com/dotnet/runtime/blob/main/src/libraries/System.Text.Json/src/System/Text/Json/Serialization/JsonSerializer.Read.String.cs)のDeserializeメソッドから）：

```csharp
/// <summary>
/// Parse the text representing a single JSON value into a <typeparamref name="TValue"/>.
/// </summary>
/// <returns>A <typeparamref name="TValue"/> representation of the JSON value.</returns>
/// <param name="json">JSON text to parse.</param>
/// <param name="options">Options to control the behavior during parsing.</param>
/// <exception cref="System.ArgumentNullException">
/// <paramref name="json"/> is <see langword="null"/>.
/// </exception>
/// <exception cref="JsonException">
/// The JSON is invalid.
///
/// -or-
///
/// <typeparamref name="TValue" /> is not compatible with the JSON.
///
/// -or-
///
/// There is remaining data in the string beyond a single JSON value.</exception>
/// <exception cref="NotSupportedException">
/// There is no compatible <see cref="System.Text.Json.Serialization.JsonConverter"/>
/// for <typeparamref name="TValue"/> or its serializable members.
/// </exception>
/// <remarks>Using a <see cref="string"/> is not as efficient as using the
/// UTF-8 methods since the implementation natively uses UTF-8.
/// </remarks>
[RequiresUnreferencedCode(SerializationUnreferencedCodeMessage)]
public static TValue? Deserialize<TValue>(string json, JsonSerializerOptions? options = null)
{
```

**C＃**では、ドキュメントコメントをコンパイラで処理してXMLドキュメントファイルを生成できます。これらのファイルはライブラリと一緒に配布できるため、Visual Studioやその他のIDEはIntelliSenseを使用して、タイプやメンバーに関する情報をすばやく表示できます。さらに、これらのファイルを[DocFx](https://dotnet.github.io/docfx/)などのツールで実行して、APIリファレンスWebサイトを生成できます。

詳しくは:

- [C＃ドキュメントコメントに推奨されるXMLタグ](https://docs.microsoft.com/dotnet/csharp/language-reference/xmldoc/recommended-tags)。

他の言語では、外部ツールが必要になる場合があります。たとえば、**Java** docコメントをJavadocツールで処理して、HTMLドキュメントファイルを生成できます。

詳しくは:

- [Javadocツールのドキュメントコメントの書き方](https://www.oracle.com/technical-resources/articles/java/javadoc-tool.html)
- [Javadocツール](https://www.oracle.com/java/technologies/javase/javadoc-tool.html#javadocdocuments)
