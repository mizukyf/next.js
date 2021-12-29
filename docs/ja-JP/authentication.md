---
description: Next.jsにおける認証パターンを学び、いくつかの例を見てみましょう。
---

# 認証

認証（authentication）はユーザーが誰であるかを検証し、認可（authorization）はユーザーが何にアクセスするかを制御します。Next.jsは異なるユースケースのために設計された、複数の認証パターンをサポートします。このページではあなたのアプリケーションの制約に基づき選択することのできるそてぞれのケースについて見ていきましょう。

## 認証パターン

あなたが選択する認証パターンがいずれであるか見極めるためにまず、あなたが望む[データ取得戦略](/docs/basic-features/data-fetching.md)について理解しましょう。しかるのち、その戦略をサポートする認証プロバイダーを選択することになります。2つの主なパターンがあります：

- [静的生成](/docs/basic-features/pages.md#static-generation-recommended)によって読み込み中状態をサーバーサイド・レンダリングし、これに続いてクライアントサイドでユーザー・データを取得します。
- ユーザー・データを[サーバーサイドで](/docs/basic-features/pages.md#server-side-rendering)取得することで、認証前状態のコンテンツが一瞬表示されてしまうのを防ぎます。

### 静的生成されるページを認証する

ブロッキング・データの要件がない場合、Next.jsはページを自動的に静的なものにします。これはつまりページ内に[`getServerSideProps`](/docs/basic-features/data-fetching.md#getserversideprops-server-side-rendering)と`getInitialProps`が存在しないケースです。これらの関数でユーザー情報を取得する代わりに、ページは読み込み中状態でサーバーサイド・レンダリングされ、その後クライアントサイドでユーザー情報を取得します。

このパターンの優れている点は、ページをグローバルなCDNを通じて配信し、[`next/link`](/docs/api-reference/next/link.md)によって事前読み込みさせることができるということです。実際、これによってより高速なTTI（[ページを操作可能になるまでの時間](https://web.dev/interactive/)）が得られます。

では、プロフィール・ページの例を見てみましょう。はじめにこのページは読み込み中状態を示すスケルトンとしてレンダリングされます。そしてひとたびユーザー情報のリクエストが完了すると、ユーザー名が表示されます：

```jsx
// pages/profile.js

import useUser from '../lib/useUser'
import Layout from '../components/Layout'

const Profile = () => {
  // クライアントサイドでユーザー情報を取得する
  const { user } = useUser({ redirectTo: '/login' })

  // サーバーサイド・レンダリングにより読み込み中状態が表示される
  if (!user || user.isLoggedIn === false) {
    return <Layout>Loading...</Layout>
  }

  // ユーザー情報のリクエストが完了したら、その情報を表示する
  return (
    <Layout>
      <h1>Your Profile</h1>
      <pre>{JSON.stringify(user, null, 2)}</pre>
    </Layout>
  )
}

export default Profile
```

この[例が動くさま](https://iron-session-example.vercel.app/)を見ることができます。[`with-iron-session`](https://github.com/vercel/next.js/tree/canary/examples/with-iron-session)の例を確認し、この仕組がどのようにはたらくかをご覧ください。

### サーバーサイド・レンダリングされるページを認証する

ページが[`getServerSideProps`](/docs/basic-features/data-fetching.md#getserversideprops-server-side-rendering)という名前の `async`関数をエクスポートしている場合、Next.jsはこのページを`getServerSideProps`が返すデータを使用してリクエストの都度事前レンダリングします。

```jsx
export async function getServerSideProps(context) {
  return {
    props: {}, // このデータはpropsとしてページ・コンポーネントに渡される
  }
}
```

それでは、[サーバーサイド・レンダリング](/docs/basic-features/pages#server-side-rendering)を使用するようにプロフィール・ページの例を修正しましょう。もしセッションが存在したら、`user`はpropsの1項目としてページの`Profile`コンポーネントに渡されます。[この例](https://next-with-iron-session.vercel.app/)では読み込み中状態を示すスケルトンは存在しない点に注目してください。

```jsx
// pages/profile.js

import withSession from '../lib/session'
import Layout from '../components/Layout'

export const getServerSideProps = withSession(async function ({ req, res }) {
  if (!req.session.user) {
    return {
      redirect: {
        destination: '/login',
        permanent: false,
      },
    }
  }

  return {
    props: { user },
  }
})

const Profile = ({ user }) => {
  // ユーザー情報を表示する。読み込み中状態の表示は必要ない。
  return (
    <Layout>
      <h1>Your Profile</h1>
      <pre>{JSON.stringify(user, null, 2)}</pre>
    </Layout>
  )
}

export default Profile
```

このパターンの優れている点は、認証前の状態のコンテンツがちらっと見えてしまうことを防止できるということです。これは重要なことですが、`getServerSideProps`でユーザー・データを取得するということは、あなたの認証プロバイダーが結果を返すまで、レンダリングがブロックされてしまうということです。このボトルネックを作ってしまうのを防ぎ、TTFB（[最初の1バイトが配信されるまでの時間](https://web.dev/time-to-first-byte/)）を改善するためには、あなたのアプリの認証情報検索ロジックを高速なものとする必要があります。さもなくば、[静的生成](#authenticating-statically-generated-pages)のパターンを検討すべきです。

## 認証プロバイダー

認証パターンについて論じ終えたので、今度は個別の認証プロバイダーについて確認し、Next.jsとともにそれらを利用する方法について見てみましょう。

### あなた自身のデータベースを用意する

<details open>
  <summary><b>例</b></summary>
  <ul>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/with-iron-session">with-iron-session</a></li>
    <li><a href="https://github.com/nextauthjs/next-auth-example">next-auth-example</a></li>
  </ul></details>

もしユーザー・データを含むデータベースがすでに存在しているなら、プロバイダーに依存しないオープンソースのソリューションを利用したいと望むでしょう。

- ローレベルで、暗号化されており、ステートレスなセッション・ユーティリティをお望みでしたら、[`iron-session`](https://github.com/vercel/next.js/tree/canary/examples/with-iron-session)を使用してください。
- 組み込みのプロバイダー（Google、Facebook、GitHubなど）、JWT、JWE、Eメールとパスワード、マジック・リンク、その他の機能をフル装備した認証システムをお望みでしたら、[`next-auth`](https://github.com/nextauthjs/next-auth-example)を使用してください。

いずれのライブラリも両方の認証パターンをサポートしています。[Passport](http://www.passportjs.org/)に興味がおありでしたら、セキュアで暗号化されたクッキーを利用する例もご用意しています。

- [with-passport](https://github.com/vercel/next.js/tree/canary/examples/with-passport)
- [with-passport-and-next-connect](https://github.com/vercel/next.js/tree/canary/examples/with-passport-and-next-connect)

### その他のプロバイダー

その他の認証プロバイダーを利用する例については、[examplesフォルダー](https://github.com/vercel/next.js/tree/canary/examples)をご覧になってください：

<details open>
  <summary><b>例</b></summary>
  <ul>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/with-firebase-authentication">with-firebase-authentication</a></li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/with-magic">with-magic</a></li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/auth0">auth0</a></li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/with-supabase-auth-realtime-db">with-supabase-auth-realtime-db</a></li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/with-userbase">with-userbase</a></li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/with-supertokens">with-supertokens</a></li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/with-nhost-auth-realtime-graphql">with-nhost-auth-realtime-graphql</a></li>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/with-clerk">with-clerk</a></li>
  </ul></details>

## 関連情報

次にすべきことについての情報を知りたい方には、次のセクションをお読みになることをおすすめします：

<div class="card">
  <a href="/docs/basic-features/pages.md">
    <b>ページ：</b>
    <small>Next.jsにおけるページとその相異なる事前レンダリング方法についてより詳しく学びましょう。</small>
  </a>
</div>

<div class="card">
  <a href="/docs/basic-features/data-fetching.md">
    <b>データ・フェッチ：</b>
    <small>Next.jsにおけるデータ取得についてさらに詳しく学びましょう。</small>
  </a>
</div>
