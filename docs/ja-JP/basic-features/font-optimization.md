---
description: Next.jsは組み込みのWebフォント最適化をサポートし、インライン・フォントCSS化を行います。ここでより詳しく学びましょう。
---

# フォント最適化

バージョン**10.2**以降、Next.jsは組み込みのWebフォント最適化の機能を有しています。

Next.jsはデフォルトで、ビルド時に自動的にインライン・フォントCSS化を行い、フォント宣言を取得するために追加のロード時間が発生するのを防ぎます。これにより[First Contentful Paint（FCP）](https://web.dev/fcp/)と[Largest Contentful Paint（LCP）](https://vercel.com/blog/core-web-vitals#largest-contentful-paint)が向上します。例えば：

```js
// 最適化前
<link
  href="https://fonts.googleapis.com/css2?family=Inter"
  rel="stylesheet"
/>

// 最適化後
<style data-href="https://fonts.googleapis.com/css2?family=Inter">
  @font-face{font-family:'Inter';font-style:normal...
</style>
```

## 使用方法

あなたのNext.jsアプリケーションにWebフォントを追加する場合は、`next/head`をオーバーライドしてください。例えば、次のようにして特定のページにフォントを追加することができます：

```js
// pages/index.js

import Head from 'next/head'

export default function IndexPage() {
  return (
    <div>
      <Head>
        <link
          href="https://fonts.googleapis.com/css2?family=Inter&display=optional"
          rel="stylesheet"
        />
      </Head>
      <p>こんにちは皆さん！</p>
    </div>
  )
}
```

アプリケーション全体にわたってフォント追加を行いたい場合は[独自の`Document`](/docs/advanced-features/custom-document.md)を作成します。

```js
// pages/_document.js

import Document, { Html, Head, Main, NextScript } from 'next/document'

class MyDocument extends Document {
  render() {
    return (
      <Html>
        <Head>
          <link
            href="https://fonts.googleapis.com/css2?family=Inter&display=optional"
            rel="stylesheet"
          />
        </Head>
        <body>
          <Main />
          <NextScript />
        </body>
      </Html>
    )
  }
}

export default MyDocument
```

自動的なWebフォントの最適化の機能は現在のところGoogleフォントとTypekitをサポートしています。その他のフォント・プロバイダーのサポートもまもなくです。私たちはまた[読み込み戦略](https://github.com/vercel/next.js/issues/21555)と`font-display`値に対する制御方法の追加も計画しています。

より詳しい情報を必要とされる方は[Googleフォント表示](https://nextjs.org/docs/messages/google-font-display)を参照ください。

## 最適化を無効にする

Next.jsによるフォントの最適化を望まない場合は、オプトアウトできます。

```js
// next.config.js

module.exports = {
  optimizeFonts: false,
}
```

## 関連情報

次にすべきことについての情報を知りたい方には、次のセクションをお読みになることをおすすめします：

<div class="card">
  <a href="/docs/advanced-features/custom-document.md">
    <b>独自のDocument</b>
    <small>あなたのアプリケーションのhtmlタグやbodyタグを強化する方法を学びましょう。</small>
  </a>
</div>
