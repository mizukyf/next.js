---
description: Next.jsはAPIルートをサポートします。Next.jsアプリと一緒にAPIを構築できるのです。ここではそれがどのように機能するのか学びましょう。
---

# APIルート

<details open>
  <summary><b>例</b></summary>
  <ul>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/api-routes">基本的なAPI Routesの例</a></li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/api-routes-middleware">ミドルウェアでAPI Routesを用いる例</a></li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/api-routes-graphql">GraphQLでAPI Routesを用いる例</a></li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/api-routes-rest">RESTでAPI Routesを用いる例</a></li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/api-routes-cors">CORSとともにAPI Routesを用いる例</a></li>
  </ul></details>

APIルートはNext.jsによりあなたの**API**を構築するためのソリューションを提供します。

`pages/api`フォルダ内部のファイルはいずれも`/api/*`というパスにマッピングされ、`page`ではなくAPIエンドポイントとして取り扱われます。それらはサーバーサイドのコードにのみバンドルされ、クライアントサイドのバンドルのサイズを増加させることがありません。

例えば、次に示すAPIルート`pages/api/user.js`はステータス・コード`200`とともに`json`のレスポンスを返します：

```js
export default function handler(req, res) {
  res.status(200).json({ name: '山田 太郎' })
}
```

あるAPIルートを機能させるためには、関数（**リクエスト・ハンドラ**として知られる）をデフォルト・エクスポートする必要があります。この関数は次のパラメータを取ります：

- `req`：いくつかの[組み込みのミドルウェア](/docs/api-routes/api-middlewares.md)を追加された[http.IncomingMessage](https://nodejs.org/api/http.html#http_class_http_incomingmessage)のインスタンス
- `res`：いくつかの[ヘルパー関数](/docs/api-routes/response-helpers.md)を追加された[http.ServerResponse](https://nodejs.org/api/http.html#http_class_http_serverresponse)のインスタンス

1つのAPIルートで複数の異なるHTTPメソッドを処理するため、リクエスト・ハンドラの中で`req.method`を利用することができます：

```js
export default function handler(req, res) {
  if (req.method === 'POST') {
    // POSTリクエストを処理する
  } else {
    // その他のHTTPメソッドを処理する
  }
}
```

APIエンドポイントから値を取得する方法については、このセクションの冒頭で紹介した例をご覧になってみてください。

## ユースケース

新しいプロジェクトではあなたはすべてのAPIをAPIルートを使って構築することができます。既成のAPIをお持ちの場合は、当該のAPIをAPIルートを通じて呼び出したりする必要はありません。APIルートに関してその他いくつかのユースケースは：

- 外部サービスのURLをマスキングする（例えば、`https://company.com/secret-url`の代わりに`/api/secret`を利用するようにする）
- 外部サービスに安全にアクセスするため、サーバー上で[環境変数](/docs/basic-features/environment-variables.md)を利用する。

## 注意事項

- APIルートは[CORSヘッダーを指定しません](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)。これはつまりデフォルトでは**same-origin only**であるということです。この挙動は[CORSミドルウェア](/docs/api-routes/api-middlewares.md#connectexpress-middleware-support)でリクエスト・ハンドラをラッピングすることでカスタマイズすることができます。
- API ルートを[`next export`](/docs/advanced-features/static-html-export.md)から利用することはできません。

## 関連情報

次にすべきことについての情報を知りたい方には、次のセクションをお読みになることをおすすめします：

<div class="card">
  <a href="/docs/api-routes/api-middlewares.md">
    <b>APIミドルウェア：</b>
    <small>リクエストを処理するための組み込みのミドルウェアについて学びましょう。</small>
  </a>
</div>

<div class="card">
  <a href="/docs/api-routes/response-helpers.md">
    <b>レスポンス・ヘッダー：</b>
    <small>レスポンスを処理するための組み込みのメソッドについて学びましょう。</small>
  </a>
</div>

<div class="card">
  <a href="/docs/basic-features/typescript.md#api-routes">
    <b>TypeScript：</b>
    <small>APIルートのコードにTypeScriptを導入しましょう。</small>
  </a>
</div>
