---
description: Next.jsは組み込みのnext/scriptコンポーネントによりサードパーティのスクリプト読み込みの最適化を手助けします。
---

# Scriptコンポーネント

<details>
  <summary><b>例</b></summary>
  <ul>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/script-component">Scriptコンポーネントの例</a></li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/with-google-tag-manager">Google Tag Managerの例</a></li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/with-google-analytics">Google Analyticsの例</a></li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/with-facebook-pixel">Facebook Pixelの例</a></li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/with-clerk">Clerkの例</a></li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/with-segment-analytics">Segment Analyticsの例</a></li>
  </ul></details>

<details>
  <summary><b>バージョン履歴</b></summary></details>

バージョン | 変更点
--- | ---
`v11.0.0` | `next/script`が導入された。



Next.jsのScriptコンポーネント──[`next/script`](/docs/api-reference/next/script.md)──はHTML標準の`<script>`要素を拡張したものです。このコンポーネントは開発者がサードパーティのスクリプトの読み込み優先順位を設定することを可能にします。`next/head`に直接追加する必要はなく、アプリケーションのどこにでも配置できます。読み込みパフォーマンスを節約しながら、しかも開発者の時間の節約にも貢献するのです。

```jsx
import Script from 'next/script'

export default function Home() {
  return (
    <>
      <Script src="https://www.google-analytics.com/analytics.js" />
    </>
  )
}
```

## 概観

Webサイトは、アクセス解析、リスティング広告、カスタマー・サポート・ウィジェット、そして同意管理など、種々の機能性を追加するためにしばしばサードパーティ・スクリプトを利用しています。しかし、それによりユーザと開発者の双方の体験に悪影響を及ぼしてしまうことがあります：

- サードパーティ・スクリプトによっては読み込み性能が悪く、ユーザ体験を低下させてしまうものもあります。それが画面描画をブロックしてしまい、その他のページ・コンテンツの読み込みを遅らせてしまう場合はとくにそうです。
- 開発者は、最適な読み込みを実現するためにアプリケーション内のどこにサードパーティ・スクリプトを配置するか、その決定のためにしばしば奮闘させられています。

Scriptコンポーネントは読み込み戦略の最適化に注意を払いながら、開発者にはアプリケーション内のどこにでもサードパーティ・スクリプトを配置できるようにしてやることで、問題解決を簡単にします。

## 使用方法

アプリケーションにサードパーティ・スクリプトを追加するには、`next/script` コンポーネントをインポートします：

```jsx
import Script from 'next/script'
```

### 戦略

`next/script`の`strategy`プロパティを使用することでサードパーティ・スクリプトの読み込みタイミングを決定します：

```jsx
<Script src="https://connect.facebook.net/en_US/sdk.js" strategy="lazyOnload" />
```

3つの異なる読み込み戦略を利用できます：

- `beforeInteractive`：ページが操作可能になる前に読み込みます。
- `afterInteractive`（**デフォルト**）：ページが操作可能になった直後に読み込みます。
- `lazyOnload`：アイドル時間に読み込みます。

#### beforeInteractive戦略

`beforeInteractive`戦略により読み込まれるスクリプトはサーバから配信される初期HTMLコードの中に注入され、自身をバンドルしたJavaScriptが実行される前に実行されます。この戦略はページが操作可能になる前に取得され実行される必要のある重大なスクリプトに利用すべきものです。

```jsx
<Script
  src="https://cdn.jsdelivr.net/npm/cookieconsent@3/build/cookieconsent.min.js"
  strategy="beforeInteractive"
/>
```

この戦略によりできる限り速やかに読み込まれなくてはならないスクリプトの例：

- ボット検知スクリプト
- クッキー同意管理スクリプト

#### afterInteractive戦略

`afterInteractive`戦略により読み込まれるスクリプトはクライアントサイドで注入され、Next.jsによるページのハイドレーションの後に実行されます。この戦略は「できる限り速やかに読み込む必要がある」というほどではない、ページが操作可能になった直後に取得され実行されればよいスクリプトに利用すべきものです。

```jsx
<Script
  strategy="afterInteractive"
  dangerouslySetInnerHTML={{
    __html: `
    (function(w,d,s,l,i){w[l]=w[l]||[];w[l].push({'gtm.start':
    new Date().getTime(),event:'gtm.js'});var f=d.getElementsByTagName(s)[0],
    j=d.createElement(s),dl=l!='dataLayer'?'&l='+l:'';j.async=true;j.src=
    'https://www.googletagmanager.com/gtm.js?id='+i+dl;f.parentNode.insertBefore(j,f);
    })(window,document,'script','dataLayer', 'GTM-XXXXXX');
  `,
  }}
/>
```

ページが操作可能になった直後に読み込むスクリプトとして好適なものの例：

- タグ・マネージャーのスクリプト
- アクセス解析のスクリプト

#### lazyOnload戦略

`lazyOnload`戦略を使用するスクリプトはすべてのページ・リソースが読み込まれた後、アイドル時間のうちに読み込まれます。この戦略は、ページが操作可能になる事前もしくは直後に読み込まれる必要がなく、バックグラウンドで実行されるか優先度の低いスクリプトに利用すべきものです。

```jsx
<Script src="https://connect.facebook.net/en_US/sdk.js" strategy="lazyOnload" />
```

速やかに読み込む必要がなく、かつ遅延読み込み可能なスクリプトの例：

- チャット・サポート・プラグインのスクリプト
- SNSウィジェットのスクリプト

### インライン・スクリプト

Scriptコンポーネントは、インライン・スクリプト、もしくは外部ファイルから読み込まれるのでないスクリプトもまたサポートします。これらのスクリプトは波括弧の中に記述します：

```jsx
<Script id="show-banner" strategy="lazyOnload">
  {`document.getElementById('banner').classList.remove('hidden')`}
</Script>
```

あるいは、`dangerouslySetInnerHTML`プロパティを使って記述します：

```jsx
<Script
  id="show-banner"
  dangerouslySetInnerHTML={{
    __html: `document.getElementById('banner').classList.remove('hidden')`,
  }}
/>
```

インライン・スクリプトのためにScriptコンポーネントを利用する時、注意すべき制限事項が2つあります：

- `afterInteractive`と`lazyOnload`戦略のみが利用可能です。`beforeInteractive`戦略は外部スクリプトの中身をHTTPレスポンスにより返される初期HTMLの中に注入します。インライン・スクリプトはそれと同じことをしています。これが<code>インライン・スクリプトに対して&lt;code data-md-type="codespan"&gt;beforeInteractive</code>戦略を利用することができない理由です。
- Next.jsがコードを追跡し最適化するために必ず`id`属性を定義しなくてはなりません。

### ロード後に実行する（`onLoad`）

サードパーティ・スクリプトの中には、スクリプトの読み込みが終わった後、コンテンツを初期化したり関数を呼び出したりするためにJavaScriptコードを実行しなくてはならないものもあります。`beforeInteractive`もしくは`afterInteractive`を読み込み戦略として指定していた場合、`onLoad`プロパティを利用することで読み込み完了後にコードを実行することができます：

```jsx
import { useState } from 'react'
import Script from 'next/script'

export default function Home() {
  const [stripe, setStripe] = useState(null)

  return (
    <>
      <Script
        id="stripe-js"
        src="https://js.stripe.com/v3/"
        onLoad={() => {
          setStripe({ stripe: window.Stripe('pk_test_12345') })
        }}
      />
    </>
  )
}
```

### 付加的な属性

 [`nonce`](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/nonce)や[カスタムデータ属性](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/data-*)のように、`<script>`要素に設定できるDOM属性ではあるもののScriptコンポーネントでは利用しないものが多数あります。これらの付加的な属性は、最終的な、最適化された、ページに出力される`<script>`要素に対して自動的に適用されます。

```jsx
import Script from 'next/script'

export default function Home() {
  return (
    <>
      <Script
        src="https://www.google-analytics.com/analytics.js"
        id="analytics"
        nonce="XUENAJFW"
        data-test="analytics"
      />
    </>
  )
}
```
