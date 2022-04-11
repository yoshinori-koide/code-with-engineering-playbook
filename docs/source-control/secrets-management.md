# ソース管理におけるシークレットの操作

シークレットの漏洩を回避する最善の方法は、シークレットをローカル/プライベートファイルに保存し、[.gitignore](https://git-scm.com/docs/gitignore)ファイルを使用してgitトラッキングから除外することです。たとえば、次のパターンでは、拡張子が`.private.config`のすべてのファイルが除外されます。

```bash
# remove private configuration
*.private.config
```

ソース管理におけるクレデンシャルとシークレットの適切な管理、およびソース管理へのシークレットの偶発的なコミットの処理の詳細については、言語ごとに分割された詳細情報が記載された[シークレット管理](../continuous-delivery/secrets-management/README.md)ドキュメントを参照してください。

追加のセキュリティ対策として、CI/CDパイプラインに[クレデンシャルスキャン](../continuous-integration/dev-sec-ops/secret-management/recipes/detect-secrets.md)を適用します。
