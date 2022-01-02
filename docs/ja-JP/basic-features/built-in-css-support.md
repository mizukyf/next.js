---
description: Next.jsはグローバルCSSないしCSSモジュール、JS中にCSSを埋め込む`styled-jsx` の利用、ないしその他のJS内でCSSを用いるソリューションをサポートしています！ ここで詳しく学びましょう。
---

# 組み込みのCSSサポート

<details open>
  <summary><b>例</b></summary>
  <ul>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/basic-css">基本的なCSSの例</a></li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/with-tailwindcss">Tailwind CSSを使用する例</a></li>
  </ul></details>

Next.jsではJavascriptファイルからCSSファイルをインポートすることができます。このようなことが可能なのは、Next.jsがJavascriptの[`import`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import)の概念を拡張しているからです。

## グローバル・スタイルシートを追加する

あなたのアプリにスタイルシートを追加するには、`pages/_app.js`の中でCSSファイルをインポートします。

例えば、`styles.css`という名前の次のようなスタイルシートについて考えてみましょう：

```css
body {
  font-family: 'SF Pro Text', 'SF Pro Icons', 'Helvetica Neue', 'Helvetica',
    'Arial', sans-serif;
  padding: 20px 20px 60px;
  max-width: 680px;
  margin: 0 auto;
}
```

もしまだ存在しないようでしたら、[`pages/_app.js`を作成しましょう。しかるのちに、`styles.css`ファイルを](/docs/advanced-features/custom-app.md)[`import`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import)します。

```jsx
import '../styles.css'

// 新しい `pages/_app.js` ファイルにはこのデフォルト・エクスポートが必要
export default function MyApp({ Component, pageProps }) {
  return <Component {...pageProps} />
}
```

これらのスタイル（`styles.css`）はあなたのアプリのすべてのページとコンポーネントに適用されます。スタイルシートのグローバルな性質のため、そしてそれに伴う競合を回避するために、**[`pages/_app.js`](/docs/advanced-features/custom-app.md)の内側でのみインポート**しましょう。

開発モードでは、この方法で適用されたスタイルシートは、あなたがファイルを更新すると、ホット・リロードされます。つまり、あなたのアプリはステートを維持することができます。

プロダクション・モードでは、すべてのCSSファイルは単一のミニファイされた`.css`ファイルへと自動的に結合されます。

### `node_modules`からスタイルシートを読み込む

Next.jsバージョン**9.5.4**以降、アプリケーションのどこであっても`node_modules`のCSSファイルを読み込むことが可能になりました。

 `bootstrap`や`nprogress`のような、グローバルなスタイルシートについては、`pages/_app.js`ファイルの内側でのみ読み込みをするようにすべきです。例えば：

```jsx
// pages/_app.js
import 'bootstrap/dist/css/bootstrap.css'

export default function MyApp({ Component, pageProps }) {
  return <Component {...pageProps} />
}
```

サードパーティのコンポーネントが必要とするCSSを読み込む場合、あなたのコンポーネントのファイルの中でそれを行うことができます。例えば：

```tsx
// components/ExampleDialog.js
import { useState } from 'react'
import { Dialog } from '@reach/dialog'
import VisuallyHidden from '@reach/visually-hidden'
import '@reach/dialog/styles.css'

function ExampleDialog(props) {
  const [showDialog, setShowDialog] = useState(false)
  const open = () => setShowDialog(true)
  const close = () => setShowDialog(false)

  return (
    <div>
      <button onClick={open}>Open Dialog</button>
      <Dialog isOpen={showDialog} onDismiss={close}>
        <button className="close-button" onClick={close}>
          <VisuallyHidden>Close</VisuallyHidden>
          <span aria-hidden>×</span>
        </button>
        <p>こんにちは。私はダイアログです。</p>
      </Dialog>
    </div>
  )
}
```

## コンポーネント・レベルのCSSを追加する

Next.jsは`[name].module.css`というファイル命名規約を用いる[CSSモジュール](https://github.com/css-modules/css-modules)をサポートしています。

CSSモジュールは、自動的に作成されたユニークなクラス名によりローカルなスコープを付与されます。これにより同じCSSクラス名を競合の心配なく複数のファイルで利用することができます。

この振る舞いのために、CSSモジュールをコンポーネント・レベルのCSSを読み込むのに理想的な方法です。CSSモジュール・ファイルは**あなたのアプリケーションのどこであっても読み込むことが可能**です。

例えば、`components/`フォルダー内に置かれた再利用可能な`Button`コンポーネントについて考えてみましょう：

まず、`components/Button.module.css`を次の内容で作成しましょう：

```css
/*
クラス .error {} ステートメントが他の `.css` ファイルや `.module.css` の中のステートメントと競合する心配はない！
*/
.error {
  color: white;
  background-color: red;
}
```

次に、`components/Button.js`を作成し、上記CSSファイルを読み込んで利用しましょう：

```jsx
import styles from './Button.module.css'

export function Button() {
  return (
    <button
      type="button"
      // 読み込んだ `styles` オブジェクトのプロパティとして、
      // "error"クラスにアクセスすることに注意。
      className={styles.error}
    >
      Destroy
    </button>
  )
}
```

CSSモジュールは*任意選択の機能*であり、`.module.css`という拡張子のファイルについてのみ可能な機能です。正規の`<link>`によるスタイルシートやグローバルなCSSもまたサポートされています。

プロダクション・モードでは、すべてのCSSモジュール・ファイルは自動的にコード結合され、**多数のミニファイされコード分割された** `.css`ファイルとなります。これらの`.css`ファイルはアプリケーションのホットパス（パフォーマンスに大きく影響する箇所）となるので、ページの描画に必要な最小限の量のCSSが読み込まれます。

## SASSのサポート

Next.jsでは`.scss`と`.sass`という2つの拡張子を用いるSASSの読み込みもできます。CSSモジュールの仕組みにより、`.module.scss`もしくは`.module.sass` という拡張子を使って、コンポーネント・レベルのSASSを利用することもできます。

Next.jsの組み込みCSSサポートを使う前に、`sass`をインストールするのをお忘れなく：

```bash
npm install sass
```

SASSのサポートは前述した組み込みのCSSサポートと同じメリットと制約を伴います。

> **注意**：SASSのサポートは[2つの異なる構文を持っており](https://sass-lang.com/documentation/syntax)、それぞれに対応する拡張子があります。拡張子`.scss`を用いる場合は[SCSS構文](https://sass-lang.com/documentation/syntax#scss)が、`.sass`を用いる場合は[インデントされた構文（"SASS"）](https://sass-lang.com/documentation/syntax#the-indented-syntax)が必要になります。
>
> 選択に自信が持てない場合は、拡張子`.scss`ではじめてください。SCSS構文はCSSのスーパーセットなので使用にあたっての追加の学習が不要ですが、インデントされた構文（"SASS"）はそうではありません。

### SASSのオプションをカスタマイズする

SASSコンパイラを設定変更したい場合は、`next.config.js`の中で`sassOptions`を使用してください。

例えば`includePaths`を追加する場合は：

```js
const path = require('path')

module.exports = {
  sassOptions: {
    includePaths: [path.join(__dirname, 'styles')],
  },
}
```

### SASS変数

Next.jsはCSSモジュール・ファイルからエクスポートされたSASS変数もサポートします：

例えば、`primaryColor`というエクスポートされたSASS変数の場合は：

```scss
/* variables.module.scss */
$primary-color: #64FF00

:export {
  primaryColor: $primary-color
}
```

```js
// pages/_app.js
import variables from '../styles/variables.module.scss'

export default function MyApp({ Component, pageProps }) {
  return (
    <Layout color={variables.primaryColor}>
      <Component {...pageProps} />
    </Layout>
  )
}
```

## JS内でCSSを用いるソリューション

<details>
  <summary><b>例</b></summary>
  <ul>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/with-styled-jsx">Styled JSX</a></li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/with-styled-components">Styled Components</a></li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/with-emotion">Emotion</a></li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/with-linaria">Linaria</a></li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/with-tailwindcss-emotion">Tailwind CSS と Emotion の組み合わせ</a></li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/with-styletron">Styletron</a></li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/with-cxs">Cxs</a></li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/with-aphrodite">Aphrodite</a></li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/with-fela">Fela</a></li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/with-stitches">Stitches</a></li>
  </ul></details>

JS内でCSSを用いる既存のソリューションもまた利用可能です。もっともシンプルなものの1つは、インライン・スタイルです：

```jsx
function HiThere() {
  return <p style={{ color: 'red' }}>やあ、こんにちは</p>
}

export default HiThere
```

私たちは隔離されスコープを限られたCSSのサポートを強化するために[styled-jsx](https://github.com/vercel/styled-jsx)をNext.jsにバンドルしました。その狙いはWebコンポーネント（シャドウDOM）に似た  "シャドウCSS" をサポートすることにありますが、残念ながら[サーバーサイド・レンダリングをサポートしておらず、JSのみで有効](https://github.com/w3c/webcomponents/issues/71)です。

上述したJS内でCSSを用いるその他の人気のソリューション（Styled Componentsのような）の例もご確認ください。

`styled-jsx`を利用するコンポーネントは次のようになります：

```jsx
function HelloWorld() {
  return (
    <div>
      こんにちはみなさん
      <p>scoped!</p>
      <style jsx>{`
        p {
          color: blue;
        }
        div {
          background: red;
        }
        @media (max-width: 600px) {
          div {
            background: blue;
          }
        }
      `}</style>
      <style global jsx>{`
        body {
          background: black;
        }
      `}</style>
    </div>
  )
}

export default HelloWorld
```

より多くの例については[styled-jsxのドキュメント](https://github.com/vercel/styled-jsx)をご覧ください。

## よくある質問

### JavaScriptが無効化されていても機能する？

はい。JavaScriptが無効化されている場合でも、プロダクション・ビルド（`next start`）においてCSSが読み込まれます。開発モードでは、[Fast Refresh](https://nextjs.org/blog/next-9-4#fast-refresh)に伴う最良の開発者体験のために、JavaScriptを有効化していただく必要があります。

## 関連情報

次にすべきことについての情報を知りたい方には、次のセクションをお読みになることをおすすめします：

<div class="card">
  <a href="/docs/advanced-features/customizing-postcss-config.md">
    <b>PostCSSの設定をカスタマイズする：</b>
    <small>PostCSSの設定とNext.jsにより追加されたプラグインを拡張してみましょう。</small>
  </a>
</div>
