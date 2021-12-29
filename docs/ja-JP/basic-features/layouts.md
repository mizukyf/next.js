---
description: レイアウトを使ってNext.jsのページ同士の間でコンポーネントとステートを共有する方法を学びましょう。
---

# レイアウト

<details open>
  <summary><b>例</b></summary>
  <ul>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/layout-component">layout-component</a></li>
  </ul></details>

Reactの開発モデルのおかげで、[page](/docs/basic-features/pages.md)を一連のコンポーネントに解体することができます。これらのコンポーネントの多くがしばしばページ間で再利用されます。例えば、同じナビゲーション・バーとフッターをすべてのページに配置することができます。

```jsx
// components/layout.js

import Navbar from './navbar'
import Footer from './footer'

export default function Layout({ children }) {
  return (
    <>
      <Navbar />
      <main>{children}</main>
      <Footer />
    </>
  )
}
```

## 例

### 独自のAppと単一の共有されたレイアウト

アプリケーション全体にわたって同じ1つのレイアウトを利用する場合、[独自のApp](/docs/advanced-features/custom-app.md)を作成し、当該レイアウトであなたのアプリケーションを包み込みます。`<Layout />`コンポーネントはページ遷移の際に再利用されるので、このコンポーネントの持つステート（例えばユーザーが入力した値）は維持されます。

```jsx
// pages/_app.js

import Layout from '../components/layout'

export default function MyApp({ Component, pageProps }) {
  return (
    <Layout>
      <Component {...pageProps} />
    </Layout>
  )
}
```

### ページごとのレイアウト

複数のレイアウトが必要な場合、ページに`getLayout`プロパティを追加しましょう。このプロパティにアサインされた関数からReactコンポーネントを返すことでレイアウトを指定できます。これにより*ページごとの*レイアウトを定義することが可能になります。関数を返すことで、もしお望みならば、複雑な入れ子になったレイアウトも可能です。

```jsx
// pages/index.js

import Layout from '../components/layout'
import NestedLayout from '../components/nested-layout'

export default function Page() {
  return {
    /** あなたのコンテンツ */
  }
}

Page.getLayout = function getLayout(page) {
  return (
    <Layout>
      <NestedLayout>{page}</NestedLayout>
    </Layout>
  )
}
```

```jsx
// pages/_app.js

export default function MyApp({ Component, pageProps }) {
  // もし可能なら、ページ・レベルで定義されたレイアウトを使用する
  const getLayout = Component.getLayout || ((page) => page)

  return getLayout(<Component {...pageProps} />)
}
```

私たちは、シングルページ・アプリケーション（SPA）のユーザー体験のため、ページ遷移時にページのステート（ユーザー入力値、スクロール位置、その他）が*保存*されていることを望んでいます。

このレイアウト・パターンは状態の保存を可能にします。なぜならReactコンポーネントのツリーはページ遷移の過程で保持されるからです。コンポーネントのツリーにより、ステートの維持のためどの要素が変更されたかReactが理解できます。

> **注意**：どの要素が変更されたかReactが理解する方法、このプロセスは[調停（reconciliation）](https://reactjs.org/docs/reconciliation.html)と呼ばれます。

### TypeScriptで開発する

TypeScriptで開発をする場合、まず最初に`getLayout`を含むページの型を作成しなくてはなりません。さらに、こうして定義したページ型を使用するために`Component`プロパティをオーバーライドする独自の`AppProps`型も作成しなくてはなりません。

```tsx
// pages/index.tsx

import type { ReactElement } from 'react'
import Layout from '../components/layout'
import NestedLayout from '../components/nested-layout'

export default function Page() {
  return {
    /** あなたのコンテンツ */
  }
}

Page.getLayout = function getLayout(page: ReactElement) {
  return (
    <Layout>
      <NestedLayout>{page}</NestedLayout>
    </Layout>
  )
}
```

```tsx
// pages/_app.tsx

import type { ReactElement, ReactNode } from 'react'
import type { NextPage } from 'next'
import type { AppProps } from 'next/app'

type NextPageWithLayout = NextPage & {
  getLayout?: (page: ReactElement) => ReactNode
}

type AppPropsWithLayout = AppProps & {
  Component: NextPageWithLayout
}

export default function MyApp({ Component, pageProps }: AppPropsWithLayout) {
  // 可能ならば、ページ・レベルで定義されたレイアウトを利用する
  const getLayout = Component.getLayout ?? ((page) => page)

  return getLayout(<Component {...pageProps} />)
}
```

### データ・フェッチ

レイアウトの内部では、`useEffect`ないし[SWR](https://swr.vercel.app/)のようなライブラリを利用してクライアントサイドでデータ取得が可能です。このファイルは[Page](/docs/basic-features/pages.md)ではないので、現在のところ`getStaticProps`や`getServerSideProps`は利用できません。

```jsx
// components/layout.js

import useSWR from 'swr'
import Navbar from './navbar'
import Footer from './footer'

export default function Layout({ children }) {
  const { data, error } = useSWR('/api/navigation', fetcher)

  if (error) return <div>読み込み失敗</div>
  if (!data) return <div>読み込み中...</div>

  return (
    <>
      <Navbar links={data.links} />
      <main>{children}</main>
      <Footer />
    </>
  )
}
```

次にすべきことについての情報を知りたい方には、次のセクションをお読みになることをおすすめします：

<div class="card">
  <a href="/docs/basic-features/pages.md">
    <b>ページ：</b>
    <small>Next.jsのページについてより詳しく学びましょう。</small>
  </a>
</div>

<div class="card">
  <a href="/docs/advanced-features/custom-app.md">
    <b>独自のApp：</b>
    <small>Next.jsがページを初期化する方法についてより詳しく学びましょう。</small>
  </a>
</div>
