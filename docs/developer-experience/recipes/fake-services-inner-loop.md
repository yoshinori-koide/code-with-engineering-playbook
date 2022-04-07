# 偽のサービスの内部開発ループ

## 序章

リモートサービスの消費者は、開発サイクルがリモートサービスの開発と同期していないことに気付くことが多く、これらの消費者の開発者はリモートサービスが「追いつく」のを待っています。この問題を軽減し、内部開発ループを改善するための1つのアプローチは、モックサービスを分離して使用することです。さまざまなモックサービスオプションの詳細については、[こちら](../client-app-inner-loop.md)をご覧ください。

このドキュメントでは、偽のサービスアプローチを使用した例を提供することに焦点を当てます。

## API

サンプルAPIの場合、`/User`エンドポイントに対して作業し、そのプロパティは次の`User`ようになります。

1. id - int
2. username - string
3. firstName - string
4. lastName - string
5. email - string
6. password - string
7. phone - string
8. userStatus - int

## ツーリング

偽のサービスアプローチでは、[Json-Server](https://github.com/typicode/json-server)を使用します。Json-Serverは、REST APIを完全に偽造し、サーバーをローカルで実行する機能を提供するツールです。最小限のセットアップでCRUD機能を備えたRESTAPIを起動するように設計されています。Json-ServerにはNodeJSが必要であり、NPMを介してインストールされます。

```bash
npm install -g json-server
```

## 設定

Json-Serverを実行するには、データのソースが必要であり、データファイルに基づいてルートなどを推測します。より高度なシナリオ（カスタムルートなど）では、追加のカスタマイズを実行できることに注意してください。詳細は[こちら](https://github.com/typicode/json-server#add-custom-routes)をご覧ください。

この例では、次のデータファイルを使用します`db.json`。

```text
{
  "user": [
    {
      "id": 0,
      "username": "user1",
      "firstName": "Kobe",
      "lastName": "Bryant",
      "email": "kobe@example.com",
      "password": "superSecure1",
      "phone": "(123) 123-1234",
      "userStatus": 0
    },
    {
      "id": 1,
      "username": "user2",
      "firstName": "Shaquille",
      "lastName": "O'Neal",
      "email": "shaq@example.com",
      "password": "superSecure2",
      "phone": "(123) 123-1235",
      "userStatus": 0
    }
  ]
}
```

## 起動

Json-Serverの実行は、次のコマンドを実行するだけで起動できます。

```bash
json-server --watch src/db.json
```

実行すると、ユーザーエンドポイントはデフォルトのローカルホストポートで叩くことができます。 `http:/localhost:3000/user`

Json-Serverは、次の構文を使用して他のポートを使用するように構成できることに注意してください。

```bash
json-server --watch db.json --port 3004
```

## エンドポイント

エンドポイントは、それに対してcurlを実行することでテストでき、次のコマンドを使用して、取得するユーザーオブジェクトを絞り込むことができます。

```bash
curl http://localhost:3000/user/1
```

これは、予想どおり、次を返します。

```text
{
  "id": 1,
  "username": "user2",
  "firstName": "Shaquille",
  "lastName": "O'Neal",
  "email": "shaq@example.com",
  "password": "superSecure2",
  "phone": "(123) 123-1235",
  "userStatus": 0
}
```
