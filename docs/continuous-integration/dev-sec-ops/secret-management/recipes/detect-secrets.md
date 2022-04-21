# クレデンシャルスキャンツール： detect-secrets

## 背景

この`detect-secrets`ツールは、ヒューリスティックとルールを使用して[さまざまな](https://github.com/Yelp/detect-secrets#currently-supported-plugins)秘密をスキャンするオープンソースプロジェクトです。単純な[PythonプラグインAPI](https://github.com/Yelp/detect-secrets/blob/a9dff60/detect_secrets/plugins/base.py#L27-L49)を介して、カスタムルールとヒューリスティックでツールを拡張できます。

他のクレデンシャルスキャンツールとは異なり、`detect-secrets`は、呼び出されたときにプロジェクトのgit履歴全体をチェックしようとはせず、代わりにプロジェクトの現在の状態をスキャンします。これは、ツールが迅速に実行されることを意味し、継続的インテグレーションパイプラインでの使用に最適です。

`detect-secrets`は「ベースラインファイル」の概念、つまりリポジトリにすでに存在する既知のシークレットのリストを採用しており、実行時にこれらの既存のシークレットを無視するように構成できます。これにより、既存のプロジェクトにツールを徐々に導入することが容易になります。

ベースラインファイルは、誤検知を処理するためのシンプルで便利な方法も提供します。ベースラインファイルの誤検知をホワイトリストに登録して、ツールの今後の呼び出しで無視することができます。

## 設定

```sh
# install system dependencies: diff, jq, python3 (if on Linux-based OS)
apt-get install -y diffutils jq python3 python3-pip

# install system dependencies: diff, jq, python3 (if on Windows)
winget install Python.Python.3
choco install diffutils jq -y

# install the detect-secrets tool
python3 -m pip install detect-secrets

# run the tool to establish a list of known secrets
# review this file thoroughly and check it into the repository
detect-secrets scan > .secrets.baseline
```

## 使用法

```sh
# backup the list of known secrets
cp .secrets.baseline .secrets.new

# find all the secrets in the repository
detect-secrets scan --baseline .secrets.new $(find . -type f ! -name '.secrets.*' ! -path '*/.git*')

# if there is any difference between the known and newly detected secrets, break the build
list_secrets() { jq -r '.results | keys[] as $key | "\($key),\(.[$key] | .[] | .hashed_secret)"' "$1" | sort; }

if ! diff <(list_secrets .secrets.baseline) <(list_secrets .secrets.new) >&2; then
  echo "Detected new secrets in the repo" >&2
  exit 1
fi
```
