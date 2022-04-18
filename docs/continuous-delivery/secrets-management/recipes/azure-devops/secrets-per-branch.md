# Azure DevOps：ブランチごとの設定の管理

CI/CDに[Azure DevOps Pipelines](https://azure.microsoft.com/en-us/services/devops/pipelines/) を使用する場合、シークレット管理に組み込みの[パイプライン変数](https://docs.microsoft.com/en-us/azure/devops/pipelines/process/variables) を利用すると便利ですが、[シークレット管理](../../README.md)にパイプライン変数を使用することには欠点があります。

- *パイプライン変数は、それらを参照するコードの外部で管理されます*。これにより、ソースコードとシークレットの間にドリフトを簡単に導入できます。たとえば、コードに新しいシークレットへの参照を追加するが、パイプライン変数に追加するのを忘れる（ビルドブレークを混乱させる）、またはシークレットへの参照を削除するなどです。コードを記述し、パイプライン変数からリモート化するのを忘れると（パイプライン変数が混乱する原因になります）。

- *パイプライン変数はグローバル共有状態です*。これは、開発者がパイプライン変数に同時に変更を加えて相互にオーバーライドする場合に、混乱を招き、問題をデバッグするのが困難になる可能性があります。パイプライン変数の単一のグローバルセットがあると、環境ごとにシークレットを変更することもできなくなります（たとえば、「マスター」が本番シークレットを使用してデプロイする、「開発」がステージングシークレットを使用してデプロイするブランチベースのデプロイメントモデルを使用する場合など）。 

これらの制限に対する解決策は、プロジェクトのソースコードと一緒にGitリポジトリ内のシークレットを管理することです。[シークレット管理](../../README.md)で説明されているように、プレーンテキストでシークレットをリポジトリにチェックインしないでください。代わりに、暗号化されたバージョンのシークレットをリポジトリに追加し、CI/CDエージェントと開発者が事前共有キーを使用してローカルで使用するためにシークレットを復号化できるようにすることができます。これにより、シークレットの安全なストレージと、シークレットとコードの並列管理という、両方の長所が得られます。

```sh
# first, make sure that we never commit our plain text secrets and generate a strong encryption key
echo ".env" >> .gitignore
ENCRYPTION_KEY="$(LC_ALL=C < /dev/urandom tr -dc '_A-Z-a-z-0-9' | head -c128)"

# now let's add some secret to our .env file
echo "MY_SECRET=..." >> .env

# also update our secrets documentation file
cat >> .env.template <<< "
# enter description of your secret here
MY_SECRET=
"

# next, encrypt the plain text secrets; the resulting .env.enc file can safely be committed to the repository
echo "${ENCRYPTION_KEY}" | openssl enc -aes-256-cbc -md sha512 -pass stdin -in .env -out .env.enc
git add .env.enc .env.template
git commit -m "Update secrets"
```

CI / CDを実行しているときに、ビルドサーバーはシークレットを復号化してアクセスできるようになりました。たとえば、Azure DevOpsの場合、`ENCRYPTION_KEY`を[シークレットパイプライン変数](https://docs.microsoft.com/en-us/azure/devops/pipelines/process/variables#secret-variables)として構成してから、`azure-pipelines.yml`に次の手順を追加します。

```yaml
steps:
  - script: echo "$(ENCRYPTION_KEY)" | openssl enc -aes-256-cbc -md sha512 -pass stdin -in .env.enc -out .env -d
    displayName: Decrypt secrets
```

パイプラインの[Azure key vault に直接リンクされた変数グループ](https://docs.microsoft.com/en-us/azure/devops/pipelines/library/variable-groups?view=azure-devops&tabs=yaml#link-secrets-from-an-azure-key-vault)を使用して、すべてのシークレットを1つの場所で管理することもできます。
