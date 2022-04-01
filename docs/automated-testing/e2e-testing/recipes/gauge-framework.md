# Gauge Framework

※ オリジナル: https://microsoft.github.io/code-with-engineering-playbook/automated-testing/e2e-testing/recipes/gauge-framework/

Gauge は、E2Eテストを作成および実行するための無料のオープンソースフレームワークです。ゲージをユニークにするいくつかの重要な機能は次のとおりです。

- Markdownに基づくシンプルで柔軟で豊富な構文。
- テストコードを書くための一貫したクロスプラットフォーム/言語サポート。
- プラグインをサポートするモジュラーアーキテクチャ
- プラグインを介して拡張可能で、ハッキング可能です。
- データ駆動型の実行と外部データソースをサポートします
- 保守可能なテストスイートの作成に役立ちます
- Visual Studio Code、Intellij IDEA、IDEサポートをサポート

## 仕様とは

ゲージの仕様は、Markdown構文を使用して記述されています。例えば

```bash
# Search for the data blob

## Look for file
* Goto Azure blob
```

このテスト仕様における *Search for the data blob* の箇所は **テスト仕様の見出し**で, *Look for file* の箇所は **シナリオ** で *Goto Azure blob* というステップが含まれます。

## 実装とは

プログラミング言語を使用して、仕様のステップを実装できます。次に例を示します。

```bash
from getgauge.python import step
import os
from step_impl.utils.driver import Driver
@step("Goto Azure blob")
def gotoAzureStorage():
  URL = os.getenv('STORAGE_ENDPOINT')
  Driver.driver.get(URL)
```

Gaugeランナーは、仕様内のすべてのシナリオのステップとその実装を読み取って実行し、合格または不合格のシナリオのレポートを生成します。

```bash
# Search for the data blob

## Look for file  ✔

Successfully generated html-report to => reports/html-report/index.html
Specifications:       1 executed      1 passed        0 failed        0 skipped
Scenarios:    1 executed      1 passed        0 failed        0 skipped
```

## 手順の再利用

Gaugeは、アプリケーションのフローのテストに集中するのに役立ちます。Gaugeは、ステップを可能な限り再利用可能にすることでこれを行います。Gaugeを使用すると、プログラミング言語を使用してカスタムフレームワークを構築する必要はありません。

たとえば、ゲージステップは、引用符付きのテキストを使用して、パラメータを実装に渡すことができます。

```bash
# Search for the data blob

## Look for file
* Goto Azure blob
* Search for "store_data.csv"
```

これで、実装は次のように「store_data.csv」を使用できます。

```bash
from getgauge.python import step
import os
@step("Search for <query>")
def searchForQuery(query):
  write(query)
  press("Enter")

step("Search for <query>", (query) => {
  write(query);
  press("Enter");
```

その後、さまざまなパラメータを使用して、シナリオ内またはシナリオ間でこのステップを再利用できます。

```bash
# Search for the data blob

## Look for Store data #1
* Goto Azure blob
* Search for "store_1.csv"

## Look for Store data #2
* Goto Azure blob
* Search for "store_2.csv"
```

または、複数のステップを組み合わせて **概念** を作成できます。

```bash
# Search Azure Storage for <query>
* Goto Azure blob
* Search for "store_1.csv"
```

Search Azure Storage forの概念<query>は、仕様のステップのように使用できます

```bash
# Search for the data blob

## Look for Store data #1
* Search Azure Storage for "store_1.csv"

## Look for Store data #2
* Search Azure Storage for "store_2.csv"
```

## データ駆動型テスト Data-Driven Testing 

Gaugeは、Markdownテーブルや外部csvファイルなどを使用したデータ駆動型テストもサポートしています。

```bash
# Search for the data blob

|query    |
|---------|
|store_1  |
|store_2  |
|store_3  |

## Look for stores data
* Search Azure Storage for <query>
```

これにより、テーブル内のすべての行に対してシナリオが実行されます。

上記の例では、実装を変更せずに簡潔で柔軟になるように仕様をリファクタリングしました。

## その他の機能

これは、いくつかのゲージ機能の簡単な紹介です。次のような追加機能については、ゲージのドキュメントを参照してください。

- [レポート](https://docs.gauge.org/getting_started/view-a-report.html)
- [タグ](https://docs.gauge.org/execution.html?#filter-specifications-and-scenarios-by-using-tags)
- [並列実行](https://docs.gauge.org/execution.html#filter-specifications-and-scenarios-by-using-tags)
- [環境](https://docs.gauge.org/configuration.html#using-environments-in-a-gauge-project)
- [スクリーンショット](https://docs.gauge.org/writing-specifications.html#taking-custom-screenshots)
- [プラグイン](https://docs.gauge.org/plugin.html)
- その他 いろいろ

## ゲージのインストール

この入門ガイドでは、ゲージのコア機能について説明します。このガイドの終わりまでに、Gaugeをインストールして、最初のGaugeテスト自動化プロジェクトを作成する方法を学ぶことができます。

## WindowsOSのインストール手順

### Step 1: Windowsにゲージをインストールする

This section gives specific instructions on setting up Gauge in a Microsoft Windows environment.
Download the following [installation bundle](https://github.com/getgauge/gauge/releases/download/v1.0.6/gauge-1.0.6-windows.x86_64.exe) to get the latest stable release of Gauge.

### Step 2: Installing Gauge extension for Visual Studio Code

Follow the steps to add the Gauge Visual Studio Code plugin from the IDE

1. Install the following [Gauge extension for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=getgauge.gauge).

### Troubleshooting Installation

If, when you run your first gauge spec you receive the error of missing python packages, open the command line terminal window and run this command:

```bash
python.exe -m pip install getgauge==0.3.7 --user
```

## Installation Instructions for macOS

### Step 1: Installing Gauge on macOS

This section gives specific instructions on setting up Gauge in a macOS environment.

1. Install brew if you haven’t already: Go to the [brew website](https://brew.sh/), and follow the directions there.
2. Run the brew command to install Gauge

   ```bash
   > brew install gauge
   ```

   if HomeBrew is working properly, you should see something similar to the following:

```bash
==> Downloading https://homebrew.bintray.com/bottles/gauge-1.0.6.mojave.bottle.tar.gz
==> Downloading from https://akamai.bintray.com/45/45b496b39ee682a95ca49b36a94e8041e03fca3644e80223c36539f495fee384?__gda__=exp=1568017021~hmac=f6ca3a9
######################################################################## 100.0%
==> Pouring gauge-1.0.6.mojave.bottle.tar.gz
🍺  /usr/local/Cellar/gauge/1.0.6: 3 files, 18.5MB
```

### Step 2 : Installing Gauge extension for Visual Studio Code

Follow the steps to add the Gauge Visual Studio Code plugin from the IDE

1. Install the following [Gauge extension for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=getgauge.gauge).

### Post-Installation Troubleshooting

If, when you run your first gauge spec you receive the error of missing python packages, open the command line terminal window and run this command:

```bash
python.exe -m pip install getgauge==0.3.7 --user
```
