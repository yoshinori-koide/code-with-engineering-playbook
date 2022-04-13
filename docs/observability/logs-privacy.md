# プライバシーに関するガイダンス

## 概要

システムユーザーのプライバシーを確​​保し、GDPRなどのいくつかの規制に準拠するために、一部の種類のデータはログに存在しないようにする必要があります。これには、顧客の機密性の高い個人情報（Personal Identifiable Information,PII）、および法的に認可されていないその他のデータが含まれます。

### 推奨される方法

1. コンポーネントを分離し、機密データを記録するシステムの部分を最小限に抑えます。
2. リクエストURLは通常、プロキシとWebサーバーによってログに記録されるため、機密データをURLに含めないでください。
3. システムのデバッグにPIIデータを使用することはできるだけ避けてください。たとえば、ユーザー名の代わりにIDを使用します。
4. 構造化ログを使用し、機密性の高いプロパティの拒否リストを含めます。
5. レビュー担当者はログステートメントの読み取りをスキップするのが一般的であるため、コードレビュー中に機密データを含むログステートメントを見つけることに特別な努力を払ってください。プルリクエストテンプレートを使用している場合は、これを追加のチェックボックスとして追加できます。
6. QAまたは自動テストの組織パイプラインで、ログに機密データを検出するメカニズムを含めます。

### ツールと実装方法

ログ内の機密データの匿名化には、これらのツールと方法を使用してください。

#### アプリケーションインサイト

Application Insightsは、一部のSDKでテレメトリインターセプトを提供します。これは、`ITelemetryProcessor`インターフェイスを実装することで実行できます。ITelemetryProcessorは、テレメトリ情報をApplication Insightsに送信する前に処理し、フィルタリングや変更などの多くの状況で役立ちます。以下は、「トレース」タイプのテレメトリをインターセプトする例です。

```csharp
using Microsoft.ApplicationInsights.DataContracts;

namespace Example
{
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    internal class RedactTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            var requestTelemetry = telemetry as TraceTelemetry;
            if (requestTelemetry == null) return;
            # redact emails from the message parameter
            requestTelemetry.Message = Regex.Replace(requestTelemetry.Message, @"[^@\s]+@[^@\s]+\.[^@\s]+", "[email removed]");
        }
    }
}
```

#### Elasticスタック

Elastic Stack（以前の「ELKスタック」）では、Logstashの[フィルタープラグイン](https://www.elastic.co/guide/en/logstash/current/filter-plugins.html)によるログのインターセプトが可能です。PIIの匿名化と編集のほとんどの場合、「mutate」、「alter」、「prune」などの既存のプラグインのいくつかを使用するだけで十分な場合があります。より堅牢でカスタマイズされたユースケースとして、「ruby」プラグインを使用して、任意のRubyコードを実行できます。フィルタプラグインは、[Fluentd](https://docs.fluentd.org/filter)や[FluentBit]などの一部のLogstashの代替手段にも存在します。

#### Presidio

[Presidio](https://github.com/microsoft/presidio)はデータ保護と匿名化APIを提供します。これは、テキスト内のプライベートエンティティの高速識別および匿名化モジュールを提供します。Presidioを使用すると、事前定義またはカスタムのPII認識機能を使用して、固有表現抽出、正規表現、ルールベースのロジック、および複数の言語の関連するコンテキストでのチェックサムを活用できます。上記のログ傍受方法と一緒に使用して、機密データが適切に管理および管理されるようにすることができます。PresidioはREST HTTP API用にコンテナ化されており、Python パッケージとしてインストールして、Pythonコードから呼び出すこともできます。アプリケーションコードで匿名化を処理する代わりに、外部呼び出しを使用して両方のAPIを使用できます。たとえば、Elastic Stackは、「ru​​by」フィルタープラグインを使用してPIIの編集を処理し、REST HTTP APIでPresidioを呼び出すことができます。

`logstash.conf`

```ruby
input {
    ...
}

filter {
   ruby {
    code => 'require "open3"
             message = event.get("message")
             # Call a python script triggering Presidio analyzer and anonymizer, and printing the result.
             cmd =  "python /path/to/presidio/anonymization/script.py \"#{message}\""
             # Fetch the script's stdout
             stdin, stdout, stderr = Open3.popen3(cmd)
             # Override message with the anonymized text.
             event.set("message", stdout.read)
             filter_matched(event)'
   }
}

output {
    ...
}
```
