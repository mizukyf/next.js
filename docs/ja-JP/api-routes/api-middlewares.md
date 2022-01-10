---
description: APIルートは受信したリクエストをパースするための組み込みのミドルウェアを提供しています。ここではそれらについてより詳しく学びましょう。
---

# APIミドルウェア

<details open>
  <summary><b>例</b></summary>
  <ul>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/api-routes-middleware">ミドルウェアでAPI Routesを用いる例</a></li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/api-routes-cors">CORSとともにAPI Routesを用いる例</a></li>
  </ul></details>

APIルートは受信したリクエスト（`req`）をパースする組み込みのミドルウェアを提供しています。それらのミドルウェアは：

- `req.cookies`──リクエストにより送信されたクッキーを内容とするオブジェクトです。デフォルトでは`{}`です。
- `req.query`──[クエリ文字列](https://en.wikipedia.org/wiki/Query_string)を内容とするオブジェクトです。デフォルトでは`{}`です。
- `req.body`──`content-type`に基づきパースされたリクエスト・ボディです。ボディなしの場合は`null`です。

## 独自の設定

個々のAPIルートはデフォルトの設定を変更するために`config`オブジェクトをエクスポートすることができます。これは次のようにして行います：

```js
export const config = {
  api: {
    bodyParser: {
      sizeLimit: '1mb',
    },
  },
}
```

`api`オブジェクトはAPIルートのために可能な設定項目すべてを含みます。

`bodyParser`はリクエスト・ボディのパースを有効にしますが、ボディを`Stream`として処理したい場合にはこれを無効にすることもできます：

```js
export const config = {
  api: {
    bodyParser: false,
  },
}
```

`bodyParser.sizeLimit`はパースされるボディの最大サイズです。 [bytes](https://github.com/visionmedia/bytes.js)モジュールでサポートされているいずれのフォーマットも指定できます：

```js
export const config = {
  api: {
    bodyParser: {
      sizeLimit: '500kb',
    },
  },
}
```

`externalResolver`は、サーバーサイドで機能するNext.jsに対して、*express*や*connect*などの外部リゾルバによりそのAPIルートが処理されることを明示的に伝えるためのフラグです。これにより未解決のリクエストに対する警告を無効化することができます。

```js
export const config = {
  api: {
    externalResolver: true,
  },
}
```

## ConnectやExpressのミドルウェアのサポート

[Connect](https://github.com/senchalabs/connect)互換のミドルウェアも利用できます。

例えば、APIエンドポイントに対する[CORS設定](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)は[cors](https://www.npmjs.com/package/cors)パッケージを利用して行うことができます。

まず、`cors`をインストールしましょう：

```bash
npm i cors
# もしくは
yarn add cors
```

それでは、当該のAPIルートに`cors`を追加しましょう：

```js
import Cors from 'cors'

// CORSミドルウェアを初期化する
const cors = Cors({
  methods: ['GET', 'HEAD'],
})

// 処理継続前のミドルウェア実行を待機し、もしミドルウェア内でエラーが発生したら
// 例外をスローするためのヘルパーメソッド
function runMiddleware(req, res, fn) {
  return new Promise((resolve, reject) => {
    fn(req, res, (result) => {
      if (result instanceof Error) {
        return reject(result)
      }

      return resolve(result)
    })
  })
}

async function handler(req, res) {
  // ミドルウェアを実行する
  await runMiddleware(req, res, cors)

  // 残りのAPIロジック
  res.json({ message: 'Hello Everyone!' })
}

export default handler
```

> 完成したアプリをご覧になるには[CORSとともにAPIルートを用いる](https://github.com/vercel/next.js/tree/canary/examples/api-routes-cors)例にお進みください。

## `req`と`res`をTypeScriptで拡張する

より良い型安全性のため、`req`および`res`を拡張することはおすすめいたしません。拡張する代わりに、これらのオブジェクトと協調して働く関数を使いましょう：

```ts
// utils/cookies.ts

import { serialize, CookieSerializeOptions } from 'cookie'
import { NextApiResponse } from 'next'

/**
 * この関数は `res` オブジェクトを使用して `cookie` を設定する
 */

export const setCookie = (
  res: NextApiResponse,
  name: string,
  value: unknown,
  options: CookieSerializeOptions = {}
) => {
  const stringValue =
    typeof value === 'object' ? 'j:' + JSON.stringify(value) : String(value)

  if ('maxAge' in options) {
    options.expires = new Date(Date.now() + options.maxAge)
    options.maxAge /= 1000
  }

  res.setHeader('Set-Cookie', serialize(name, stringValue, options))
}

// pages/api/cookies.ts

import { NextApiRequest, NextApiResponse } from 'next'
import { setCookie } from '../../utils/cookies'

const handler = (req: NextApiRequest, res: NextApiResponse) => {
  // `res` を利用する純粋関数を呼び出すことで、`set-cookie` ヘッダーを追加する
  setCookie(res, 'Next.js', 'api-middleware!')
  // `set-cookie` ヘッダーを返す。動作確認のためこの値をブラウザで表示できる！
  res.end(res.getHeader('Set-Cookie'))
}

export default handler
```

これらのオブジェクトの拡張を回避できない場合は、追加のプロパティを持つ独自の型を定義する必要があります：

```ts
// pages/api/foo.ts

import { NextApiRequest, NextApiResponse } from 'next'
import { withFoo } from 'external-lib-foo'

type NextApiRequestWithFoo = NextApiRequest & {
  foo: (bar: string) => void
}

const handler = (req: NextApiRequestWithFoo, res: NextApiResponse) => {
  req.foo('bar') // 型エラーなしに `req.foo` を利用できる
  res.end('ok')
}

export default withFoo(handler)
```

このコードは安全とは言えないことを心に留めておいてください。なぜなら仮にエクスポート宣言から`withFoo()`を除去しても、コードは依然としてコンパイルできてしまうからです。
