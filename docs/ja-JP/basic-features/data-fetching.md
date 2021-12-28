---
description: Next.jsは2つの事前レンダリングのモードを持っています：静的生成とサーバーサイド・レンダリングです。ここではこれらがどのようにはたらくかを学びましょう。
---

# データ・フェッチ

> このドキュメントはNext.jsバージョン9.3以上向けのものです。もしこれより古いバージョンを使用されている場合は、[以前のドキュメント](https://nextjs.org/docs/tag/v9.2.2/basic-features/data-fetching)をご覧になってください。

<details open>
  <summary><b>例</b></summary>
  <ul>
    <li>
<a href="https://github.com/vercel/next.js/tree/canary/examples/cms-wordpress">WordPressの例</a>（<a href="https://next-blog-wordpress.vercel.app">デモ</a>）</li>
    <li>
<a href="https://github.com/vercel/next.js/tree/canary/examples/blog-starter">マークダウン・ファイルを使用するシンプルなBlog</a>（<a href="https://next-blog-starter.vercel.app/">デモ</a>）</li>
    <li>
<a href="https://github.com/vercel/next.js/tree/canary/examples/cms-datocms">DatoCMSの例</a>（<a href="https://next-blog-datocms.vercel.app/">デモ</a>）</li>
    <li>
<a href="https://github.com/vercel/next.js/tree/canary/examples/cms-takeshape">TakeShapeの例</a>（<a href="https://next-blog-takeshape.vercel.app/">デモ</a>）</li>
    <li>
<a href="https://github.com/vercel/next.js/tree/canary/examples/cms-sanity">Sanityの例</a>（<a href="https://next-blog-sanity.vercel.app/">デモ</a>）</li>
    <li>
<a href="https://github.com/vercel/next.js/tree/canary/examples/cms-prismic">Prismicの例</a>（<a href="https://next-blog-prismic.vercel.app/">デモ</a>）</li>
    <li>
<a href="https://github.com/vercel/next.js/tree/canary/examples/cms-contentful">Contentfulの例</a>（<a href="https://next-blog-contentful.vercel.app/">デモ</a>）</li>
    <li>
<a href="https://github.com/vercel/next.js/tree/canary/examples/cms-strapi">Strapiの例</a>（<a href="https://next-blog-strapi.vercel.app/">デモ</a>）</li>
    <li>
<a href="https://github.com/vercel/next.js/tree/canary/examples/cms-prepr">Preprの例</a>（<a href="https://next-blog-prepr.vercel.app/">デモ</a>）</li>
    <li>
<a href="https://github.com/vercel/next.js/tree/canary/examples/cms-agilitycms">Agility CMSの例</a>（<a href="https://next-blog-agilitycms.vercel.app/">デモ</a>）</li>
    <li>
<a href="https://github.com/vercel/next.js/tree/canary/examples/cms-cosmic">Cosmicの例</a>（<a href="https://next-blog-cosmic.vercel.app/">デモ</a>）</li>
    <li>
<a href="https://github.com/vercel/next.js/tree/canary/examples/cms-buttercms">ButterCMSの例</a>（<a href="https://next-blog-buttercms.vercel.app/">デモ</a>）</li>
    <li>
<a href="https://github.com/vercel/next.js/tree/canary/examples/cms-storyblok">Storyblokの例</a>（<a href="https://next-blog-storyblok.vercel.app/">デモ</a>）</li>
    <li>
<a href="https://github.com/vercel/next.js/tree/canary/examples/cms-graphcms">GraphCMSの例</a>（<a href="https://next-blog-graphcms.vercel.app/">デモ</a>）</li>
    <li>
<a href="https://github.com/vercel/next.js/tree/canary/examples/cms-kontent">Kontentの例</a>（<a href="https://next-blog-kontent.vercel.app/">デモ</a>）</li>
    <li><a href="https://static-tweet.vercel.app/">Static Tweetデモ</a></li>
  </ul></details>

[ページのドキュメント](/docs/basic-features/pages.md)では、Next.jsが2つの事前レンダリングの形態を持つことをご説明しました：**静的生成**と**サーバーサイド・レンダリング**です。このページでは、それぞれのケースのためのデータ取得戦略をより深く説明いたします。もしまだご覧になられていないようでしたら、まずは[ページのドキュメントを一通りお読みになる](/docs/basic-features/pages.md)ことをおすすめいたします。

事前レンダリングのためのデータ取得に利用できる3つのユニークなNext.js関数について説明いたします：

- [`getStaticProps`](#getstaticprops-static-generation)（静的生成）：**ビルド時**にデータを取得します。
- [`getStaticPaths`](#getstaticpaths-static-generation)（静的生成）：データに基づくページの事前レンダリングのため[動的ルート](/docs/routing/dynamic-routes.md)を指定します。
- [`getServerSideProps`](#getserversideprops-server-side-rendering)（サーバーサイド・レンダリング）： **リクエストの都度**データを取得します。

これに加えて、クライアントサイドでデータを取得する方法についても簡単に触れます。

## `getStaticProps`（静的生成）

<details>
  <summary><b>バージョン履歴</b></summary></details>

バージョン | 変更点
--- | ---
`v12.0.0` | `staticPageGenerationTimeout`が追加された。
`v10.0.0` | `locale`、`locales`、 `defaultLocale`、そして `notFound`オプションが追加された。
`v9.5.0` | 安定版の[インクリメンタルな静的再生成](https://nextjs.org/blog/next-9-5#stable-incremental-static-regeneration)
`v9.3.0` | `getStaticProps`が登場。



ページが`getStaticProps`という名前の `async`をエクスポートしている場合、<br>Next.jsはこの`getStaticProps`関数が返したをpropsを利用して、ビルド時に当該ページを事前レンダリングします。

```jsx
export async function getStaticProps(context) {
  return {
    props: {}, // propsとしてページ・コンポーネントに渡される
  }
}
```

`context`パラメーターは次のキーを含むオブジェクトです：

- `params`は動的ルートを利用するページのためのルート・パラメーターを内包します。例えば、ページ・ファイル名が`[id].js` の時、`params`は`{ id: ... }`のようになります。より詳しく学ぶには[動的ルーティングのドキュメント](/docs/routing/dynamic-routes.md)を参照してください。このパラメーターは後ほど説明する`getStaticPaths`とともに利用します。
- `preview`が`true`の時、ページはプレビュー・モードです。それ以外の時は`undefined`です。[プレビュー・モードのドキュメント](/docs/advanced-features/preview-mode.md)もご参照ください。
- `previewData` `setPreviewData`により設定されたプレビュー・データを内包します。[プレビュー・モードのドキュメント](/docs/advanced-features/preview-mode.md)もご参照ください。
- `locale`は現在のロケールを内包します（[国際化ルーティング](/docs/advanced-features/i18n-routing.md)を有効にしている場合）。
- `locales`はサポートされているすべてのロケールを内包します（[国際化ルーティング](/docs/advanced-features/i18n-routing.md)を有効にしている場合）。
- `defaultLocale`は設定されたデフォルト・ロケールを内包します（[国際化ルーティング](/docs/advanced-features/i18n-routing.md)を有効にしている場合）。

`getStaticProps`は次のキーを含むオブジェクトを返さなくてはなりません：

- `props` ─ **任意の**オブジェクトで、ページ・コンポーネントが受け取るpropsです。 [シリアライズ可能なオブジェクト](https://en.wikipedia.org/wiki/Serialization)である必要があります。

- `revalidate` ─ **任意の**パラメーターで、ページの再生成が生じうる経過秒数です。デフォルトは`false`です。`revalidate`が `false`の時、 ページの再検証は行われず、ページはキャッシュされた時のまま次のビルドが行われる時までキャッシュされます。[インクリメンタルな静的再生性](#incremental-static-regeneration)により詳しい説明があります。

- `notFound` ─ **任意の**真偽値で、ページに404のステータスとエラー・ページを返すことを可能にします。以下のコードはこのオプションがどのようにはたらくか示しています：

    ```js
    export async function getStaticProps(context) {
      const res = await fetch(`https://.../data`)
      const data = await res.json()

      if (!data) {
        return {
          notFound: true,
        }
      }

      return {
        props: { data }, // propsとしてページ・コンポーネントに渡される
      }
    }
    ```

    > **注意**：[`fallback: false`](#fallback-false)モードのために`notFound`は必要ありません。<br>このモードでは`getStaticPaths`が返したパスのみが事前レンダリングされます。

    > **注意**：`notFound: true`の場合、以前は正常に生成されたページであっても404エラーとなります。ユーザーにより生成されたコンテンツがそれ自身の作者により削除されたというようなユースケースをサポートするのに有効です。

- `redirect` ─ **任意の**パラメーターで、内的および外的なリソースに対するリダイレクトを可能にするものです。これは `{ destination: string, permanent: boolean }`のような形をとります。稀なケースですが、古いHTTPクライアントを正しくリダイレクトさせるために、独自のステータスを設定してやる必要があることがあります。このようなケースでは、`permanent`プロパティではなく`statusCode`プロパティが利用可能ですが、両方同時にはできません。下記のコードはこのパラメーターがどのようにはたらくか示すものです：

    ```js
    export async function getStaticProps(context) {
      const res = await fetch(`https://...`)
      const data = await res.json()

      if (!data) {
        return {
          redirect: {
            destination: '/',
            permanent: false,
          },
        }
      }

      return {
        props: { data }, // propsとしてページ・コンポーネントに渡される
      }
    }
    ```

    > **注意**：ビルド時のリダイレクトは今のところできません。もしビルド時に分かっているリダイレクトがあるのであれば[`next.config.js`](/docs/api-reference/next.config.js/redirects.md)に設定を加えてください。

> **注意**：`getStaticProps`で使用するためにトップレベル・スコープにモジュールをインポートすることができます。<br>`getStaticProps`で使用されるそれらのモジュールは[クライアントサイドのためのバンドルには含まれません](#write-server-side-code-directly)。
>
> <br>これはつまり**サーバーサイド・コードを`getStaticProps`の中に直接に**記述できるということです。これには例えばファイルシステムないしデータベースからの読み込みを行うコードが含まれます。

> **注意**：[`fetch()`](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)を`getStaticProps`の中でAPIルートを呼び出すのに利用してはいけません。そうではなくて、APIルートの内部で利用しているロジックを直接インポートして使います。このアプローチのために若干ですがリファクタリングが必要になるかもしれません。
>
> 外部APIからのデータ取得は問題ありません！

### 例

ここに示すのはCMS（コンテンツ・マネジメント・システム）からブログ投稿のリストを取得するのに`getStaticProps`を利用する例です。この例は[ページのドキュメント](/docs/basic-features/pages.md)でも出てきたものです。

```jsx
// postsの内容はgetStaticProps()によりビルド時に提供される
function Blog({ posts }) {
  return (
    <ul>
      {posts.map((post) => (
        <li>{post.title}</li>
      ))}
    </ul>
  )
}

// この関数はビルド時にサーバーサイドで実行される。
// クライアントサイドで実行されないため、直接データベースにクエリを発行することもできる。
// "技術的な詳細" のセクションも参照のこと。
export async function getStaticProps() {
  // 投稿データを取得するため外部APIのエンドポイントを呼び出す。
  // データ取得のためどんなライブラリを使ってもOK。
  const res = await fetch('https://.../posts')
  const posts = await res.json()

  // ここで{ props: { posts } }を返すことで、Blogコンポーネントは
  // `posts` をpropsの1つとしてビルド時に受け取る
  return {
    props: {
      posts,
    },
  }
}

export default Blog
```

### `getStaticProps`はどのような時に使うべきか？

`getStaticProps`は次のような場合に使うべきです：

- ページのレンダリングに必要なデータがユーザーのリクエストに先立ってビルド時点で利用可能である場合。
- データがヘッドレスCMSから提供される場合。
- データが一般公開向けのキャッシュに乗せられる（ユーザー固有でない）場合。
- ページが（SEOのために）事前レンダリングされしかも応答が非常に高速でないといけない場合──`getStaticProps`がHTMLとJSONファイルを生成し、パフォーマンス向上を目的としたCDNがそれらをキャッシュします。

### TypeScriptの場合：`GetStaticProps`を使用する

TypeScriptにより開発をする場合、`next`モジュールがエクスポートする`GetStaticProps`型を利用できます：

```ts
import { GetStaticProps } from 'next'

export const getStaticProps: GetStaticProps = async (context) => {
  // ...
}
```

ページが受け取るpropsに関して型推論の恩恵を享受したい場合、次に示すように`InferGetStaticPropsType<typeof getStaticProps>`を利用することができます：

```tsx
import { InferGetStaticPropsType } from 'next'

type Post = {
  author: string
  content: string
}

export const getStaticProps = async () => {
  const res = await fetch('https://.../posts')
  const posts: Post[] = await res.json()

  return {
    props: {
      posts,
    },
  }
}

function Blog({ posts }: InferGetStaticPropsType<typeof getStaticProps>) {
  // postsは Post[] 型と推論される
}

export default Blog
```

注意：Next.jsは静的生成にデフォルトの制限時間を持っておりこれは60秒です。この制限時間内に新しいページの生成が完了しなかった場合、もう3回の試行がなされます。4回目の試みも失敗すると、ビルドは失敗となります。この制限時間は次のようにして設定変更できます：

```js
// next.config.js
module.exports = {
  // 静的生成中、新しいページが生成されないことで
  // タイムアウトとなるまでの秒数
  staticPageGenerationTimeout: 90,
}
```

### インクリメンタルな静的再生成

<details open>
  <summary><b>例</b></summary>
  <ul>
    <li><a href="https://nextjs.org/commerce">Next.js Commerce</a></li>
    <li><a href="https://reactions-demo.vercel.app/">GitHub Reactionsのデモ</a></li>
    <li><a href="https://static-tweet.vercel.app/">Static Tweetのデモ</a></li>
  </ul></details>

<details>
  <summary><b>バージョン履歴</b></summary></details>

バージョン | 変更点
--- | ---
`v9.5.0` | ベースパスが追加された。



Next.jsではサイトのビルドの*後に*静的ページを作成したり更新したりすることもできます。インクリメンタルな静的再生成（ISR）機能は**サイト全体のリビルドを必要としない**ページ単位の静的生成を可能にします。<br>ISRを用いると、膨大なページ数を抱えるサイトに対して静的生成を適用してその恩恵を享受することができます。

先程の[`getStaticProps`の例](#simple-example)で考えてみましょう。ただし今回はインクリメンタルな静的再生成を有効化するために`revalidate`プロパティを使います：

```jsx
function Blog({ posts }) {
  return (
    <ul>
      {posts.map((post) => (
        <li>{post.title}</li>
      ))}
    </ul>
  )
}

// この関数はサーバーサイドでビルド時に呼び出される。
// 再バリデーションが有効で、かつ、新しいリクエストがやって来た場合、
// この関数はサーバーサイドのランタイムで再度呼び出される。
export async function getStaticProps() {
  const res = await fetch('https://.../posts')
  const posts = await res.json()

  return {
    props: {
      posts,
    },
    // Next.jsはこのページの再生成を試みる：
    // - 新しいリクエストがやって来たタイミングで
    // - 最大で10秒に1回の頻度で
    revalidate: 10, // 単位は秒
  }
}

// この関数はサーバーサイドでビルド時に呼び出される。
// パスがまだ生成されていない場合、
// この関数はサーバーサイドのランタイムで再度呼び出される。
export async function getStaticPaths() {
  const res = await fetch('https://.../posts')
  const posts = await res.json()

  // postsのデータを元に事前レンダリングに必要なパスを取得
  const paths = posts.map((post) => ({
    params: { id: post.id },
  }))

  // ビルド時にはこれらのパスだけを事前レンダリングする。
  // { fallback: blocking } を返すことで、パスが存在しない場合、
  // それらのページをサーバーサイドのランタイムでレンダリングするよう指示する。
  return { paths, fallback: 'blocking' }
}

export default Blog
```

ビルド時に事前レンダリングされたページに対するリクエストが来ると、最初はキャッシュされたページが表示されます。

- 当該ページに対するその後のリクエストも10秒経過する前であればキャッシュされたページを瞬時に受け取ります。
- 10秒が経過した後、新しいリクエストは依然としてキャッシュされた（鮮度の落ちた）ページを受け取ります。
- Next.jsは背後で当該ページの再生成を開始します。
- ひとたびページの生成に成功すると、Next.jsはキャッシュされた内容を無効化し更新されたページを表示します。背後で行われる再生成が失敗した場合、古いページは変更されずにそのまま残ります。

未生成のパスに対するリクエストが来ると、その時点でNext.jsは当該ページをサーバーサイドでレンダリングします。その後のリクエストはキャシュされた静的ファイルを受け取ることになります。

いかにしてグローバルに有効なキャッシュを実現しロールバックを処理するかを知るには、[インクリメンタルな静的再生成](https://vercel.com/docs/next.js/incremental-static-regeneration)についてより詳しく学んでください。

### ファイルを読み取る：`process.cwd()`を使用する

`getStaticProps`の中ではファイルシステムのファイルを直接読み取ることができます。

それを行うためにファイルのフルパスを取得する必要があります。

Next.jsはあなたのコードをコンパイルして隔離されたディレクトリに移動するので、`__dirname`は利用できません。この値はページが格納されたディレクトリと異なるパスとなります。

代わりに、 `process.cwd()`を利用します。これによりNext.jsが実行されているディレクトリのパスを得られます。

```jsx
import { promises as fs } from 'fs'
import path from 'path'

// postsはビルド時にgetStaticProps()により提供される
function Blog({ posts }) {
  return (
    <ul>
      {posts.map((post) => (
        <li>
          <h3>{post.filename}</h3>
          <p>{post.content}</p>
        </li>
      ))}
    </ul>
  )
}

// この関数はビルド時にサーバーサイドで呼び出される
// クライアントサイドでは呼び出されないので、直接データベースにクエリを実行できる。
// "技術的な詳細" のセクションを参照のこと。
export async function getStaticProps() {
  const postsDirectory = path.join(process.cwd(), 'posts')
  const filenames = await fs.readdir(postsDirectory)

  const posts = filenames.map(async (filename) => {
    const filePath = path.join(postsDirectory, filename)
    const fileContents = await fs.readFile(filePath, 'utf8')

    // ここでコンテンツを読み取ったり変換したりする。
    // 例えばマークダウン形式のファイルをHTMLに変換したりする。

    return {
      filename,
      content: fileContents,
    }
  })
  // この関数が { props: { posts } } を返すことで、Blogコンポーネントは
  // ビルド時に `posts` をpropsの1項目として受け取ることができる
  return {
    props: {
      posts: await Promise.all(posts),
    },
  }
}

export default Blog
```

### 技術的な詳細

#### ビルド時にのみ実行される

`getStaticProps`はビルド時に実行されて静的なHTMLを生成するので、この関数はクエリ・パラメーターやHTTPヘッダーのようなリクエストを受け取った時点でのみ利用可能なデータを受けることが**できません**。

#### サーバーサイドで実行されるコードを直接記述する

`getStaticProps`はサーバーサイドでのみ実行されます。決してクライアントサイドでは実行されません。ブラウザ向けに配信されるJSバンドルに含まれることもありません。ということは、データベースにクエリ実行するようなコードを記述しても、それはブラウザに送信されません。<br>データを取得するために`getStaticProps`から**APIルート**を呼び出すようなことをしてはいけません。そのようなことをするのではなく、`getStaticProps`の中にサーバーサイドで実行されるコードを直接記述できるのです。

[こちらのツール](https://next-code-elimination.vercel.app/)を使うことで、<br> to verify what Next.jsがクライアントサイド・バンドルから問題のコードを除去していることを確認できます。

#### HTMLとJSOMのいずれもが静的に生成される

`getStaticProps`を伴うページがビルド時に事前レンダリングされる時、ページのHTMLファイルに加えて、`getStaticProps`の結果を保持したJSONファイルも生成されます。

このJSONファイルは`next/link`モジュール（[ドキュメント](/docs/api-reference/next/link.md)）や`next/router`モジュール（[ドキュメント](/docs/api-reference/next/router.md)）によるクライアントサイド・ルーティングにおいて利用されます。`getStaticProps`を利用して事前レンダリングされたページに遷移する時、Next.jsはこのJSONファイル（ビルド時に計算済みの内容）を取得し、ページ・コンポーネントのpropsとして利用します。クライアントサイドのページ遷移はこのJSONのみ利用して行われ、`getStaticProps`を呼び出さ**ない**ということです。

インクリメンタルな静的生成を利用している場合、<br>`getStaticProps`はクライアントサイドのページ遷移に必要なJSONを生成するために背後で実行されます。<br>この挙動に伴って、同一ページのために複数のリクエストが生成される形になるのですが、これは意図的にそうしているものであり、エンドユーザーのパフォーマンスには影響するものではありません。

#### ページにおいてのみ許される

`getStaticProps`は**page**からのみエクスポートできます。ページ以外のファイルからエクスポートすることはできません。

Reactはページがレンダリングされるより前にすべての必要なデータを取得している必要があるというのが、この制限の理由の1つです。

加えて、必ず`export async function getStaticProps() {}`という形でエクスポートしなくてはなりません。当該ページのコンポーネントのプロパティとして`getStaticProps`を定義しても動作**しません**。

#### 開発モードにおいてはリクエストの都度実行される

開発モード（`next dev`）においては、 `getStaticProps` はリクエストの都度実行されます。

#### プレビュー・モード

静的生成を一時的に迂回し、ページ・レンダリングをビルド時ではなく**リクエスト時に**行わせたいという時があります。例えば、ヘッドレスCMSを使っていて、公開前の投稿のドラフトをプレビューしたい時などです。

Next.jsの**プレビュー・モード**機能はこのユースケースをサポートします。より詳しく学ぶには[プレビュー・モードのドキュメント](/docs/advanced-features/preview-mode.md)をご覧ください。

## `getStaticPaths`（静的生成）

<details>
  <summary><b>バージョン履歴</b></summary></details>

バージョン | 変更点
--- | ---
`v9.5.0` | 安定版の[インクリメンタルな静的再生成](https://nextjs.org/blog/next-9-5#stable-incremental-static-regeneration)
`v9.3.0` | `getStaticPaths`が導入された。



ページが動的ルート（[ドキュメント](/docs/routing/dynamic-routes.md)）を持ち、かつ、`getStaticProps`を使用する場合、ビルド時にレンダリングすべきHTMLのパスのリストが必要になります。

動的ルートを利用するページから`getStaticPaths`という名前の`async`をエクスポートすると、Next.jsは`getStaticPaths`により指定されたすべてのパスを静的に事前レンダリングします。

```jsx
export async function getStaticPaths() {
  return {
    paths: [
      { params: { ... } } // 後述の "paths" セクションを参照のこと
    ],
    fallback: true, false, or 'blocking' // 後述の "fallback" セクションを参照のこと
  };
}
```

#### `paths`キー（必須）

`paths`キーは、事前レンダリングすべきパスを定義するものです。例えば、`pages/posts/[id].js`という名前の動的ルートを利用するページがあったとしましょう。このページから`getStaticPaths`をエクスポートし、次のような`paths`を返すとします：

```js
return {
  paths: [
    { params: { id: '1' } },
    { params: { id: '2' } }
  ],
  fallback: ...
}
```

すると、Next.jsは`pages/posts/[id].js`のページ・コンポーネントを利用してビルド時に`posts/1`と`posts/2` を静的に生成します。

各`params`の値はページ名の中で使用されているパラメーターに一致している必要があります：

- ページ名が`pages/posts/[postId]/[commentId]`ならば、`params`は`postId`と`commentId`を含んでいる必要があります。
- 例えば`pages/[...slug]`のように、ページ名がcatch-allルートを使用している場合、`params`は`slug`という配列を含んでいる必要があります。 この配列が`['foo', 'bar']`ならば、Next.jsは`/foo/bar`というパスのページを静的に生成します。
- ページが任意のcatch-allルートを使用している場合、<br>パラメーターとして`null`、`[]`、`undefined`ないし`false`を渡すと、最上位のルートとなるページがレンダリングされます。例えば、ページ `pages/[[...slug]]`に対して`slug: false`を渡すと、Next.jsは`/`を静的に生成します。

#### `fallback`キー（必須）

`getStaticPaths`が返すオブジェクトは真偽値の`fallback`というプロパティを持っている必要があります。

#### `fallback: false`

`fallback`が`false`の場合、`getStaticPaths`が返した一覧にないパスへのリクエストは**404ページ**に導かれます。事前レンダリングすべきパスの数が少なく、すべての静的生成がビルド時に行われる場合はこれでよいでしょう。新しいページがめったに追加されない場合もこれが適しています。データソースに新たな項目を追加し新しいページのレンダリングが必要になったら、ビルドをしなおす必要があります。

ここで示す例は、`pages/posts/[id].js`という名前のページでブログ投稿を事前レンダリングするものです。ブログ投稿のリストはCMSから取得され、`getStaticPaths`から返されます。各ページについて、投稿データがCMSから取得されます。この例は[ページのドキュメント](/docs/basic-features/pages.md)でも取り上げられているものです。

```jsx
// pages/posts/[id].js

function Post({ post }) {
  // 投稿をレンダリングする...
}

// この関数はビルド時に呼び出される
export async function getStaticPaths() {
  // 投稿データを取得するため外部APIのエンドポイントを呼び出す
  const res = await fetch('https://.../posts')
  const posts = await res.json()

  // 投稿データを元に事前レンダリングすべきパスを取得する
  const paths = posts.map((post) => ({
    params: { id: post.id },
  }))

  // これらのパスだけがビルド時に事前レンダリングされる
  // { fallback: false } はそれ以外のパスへのアクセスが404となることを意味する
  return { paths, fallback: false }
}

// この関数もまたビルド時に呼び出される
export async function getStaticProps({ params }) {
  // paramsは投稿の `id` を内包する。
  // ルートが /posts/1 のようなものならば、params.id は 1 となる
  const res = await fetch(`https://.../posts/${params.id}`)
  const post = await res.json()

  // 投稿データをpropsを通じてページに渡す
  return { props: { post } }
}

export default Post
```

#### `fallback: true`

<details>
  <summary><b>例</b></summary>
  <ul>
    <li><a href="https://static-tweet.vercel.app">膨大な数のページを静的生成する例</a></li>
  </ul></details>

`fallback`が`true`の場合、`getStaticProps`の振る舞いが変わります：

- `getStaticPaths`から返されたパスは`getStaticProps`によりビルド時にHTMLへとレンダリングされます。
- ビルド時に生成されなかったパスへのリクエストは404ページに導かれ**ません**。<br>Next.jsはそのようなパスへの初回のリクエストがあった時点で “フォールバック” バージョンのページを配信します（詳細については後述の[“Fallback pages”](#fallback-pages)を参照のこと）。注意：この “フォールバック” バージョンはGoogleなどの検索エンジンのクローラーには配信されません。当該のパスは`blocking`モードでレンダリングされます。
- その背後で、Next.jsは要求されたHTMLとJSONを静的生成します。その中で`getStaticProps`も実行されます。
- それが終わると、ブラウザは生成されたパスのためのJSONを受け取ります。これを利用して自動的にレンダリングが行われます。ユーザーの視点から見ると、フォールバック・ページが完全なページに差し替わることになります。
- 同時に、Next.jsはこのパスを事前レンダリングされたページの一覧に加えます。同じパスに対する後続のリクエストは、ビルド時に事前レンダリングされたページに対するそれと同様に、生成済みページの配信を受けます。

> [`next export`](/docs/advanced-features/static-html-export.md)を使用する場合`fallback: true`はサポートされません。

#### フォールバック・ページ

“fallback” バージョンのページでは：

- ページのpropsは空っぽです。
- [ルーター](/docs/api-reference/next/router.md)を利用すると、`router.isFallback`が`true`かどうかチェックすることで、フォールバック・ページがレンダリングされていることを検知できます。

ここに示すのは`isFallback`を利用する例です：

```jsx
// pages/posts/[id].js
import { useRouter } from 'next/router'

function Post({ post }) {
  const router = useRouter()

  // ページが生成されていない場合、getStaticProps()の
  // 実行が完了するまで、これが表示される
  if (router.isFallback) {
    return <div>読み込み中...</div>
  }

  // 投稿をレンダリングする...
}

// この関数はビルド時に呼び出される
export async function getStaticPaths() {
  return {
    // `/posts/1` と `/posts/2` だけがビルド時に生成される
    paths: [{ params: { id: '1' } }, { params: { id: '2' } }],
    // それ以外のページについても静的生成を有効にする
    // 例えば： `/posts/3`
    fallback: true,
  }
}

// この関数もまたビルド時に呼び出される
export async function getStaticProps({ params }) {
  // paramsは投稿の `id` を内包する。
  // ルートが /posts/1 のようなものならば、params.id は 1 となる
  const res = await fetch(`https://.../posts/${params.id}`)
  const post = await res.json()

  // 投稿データをpropsを通じてページに渡す
  return {
    props: { post },
    // リクエストがやって来たら、
    // この秒数あたり最大1回、投稿を再生成する
    revalidate: 1,
  }
}

export default Post
```

#### `fallback: true`はどのような時に役立つのか？

`fallback: true`が役に立つのはデータに依存する静的ページが膨大な数にのぼる時です（非常に大きなECサイトを思い浮かべてみてください）。すべてのページを事前レンダリングしたいのですが、それをビルド時にやろうとしたら永遠に終わりません。

代わりに、少数の一部のページだけを静的に生成し、残りについては`fallback: true`を利用します。誰かがまだ生成されていないページをリクエストした時、そのユーザーは読み込み中表示をするページを目にすることになります。まもなく、`getStaticProps`が完了しページがレンダリングされます。それから後、同じページをリクエストしたすべてのユーザーは静的に事前レンダリングされたページを取得することになります。

この機能は、高速なビルドと静的生成の利点を保持したまま、すべてのユーザーに対する高速な応答を確実なものとします。

`fallback: true`は生成されたページを*更新*するものではありません。この点については[インクリメンタルな静的再生成](#incremental-static-regeneration)をご覧ください。

#### `fallback: 'blocking'`

`fallback`が`'blocking'`の場合、`getStaticPaths`が返した一覧に含まれないパスへのリクエストはHTMLの生成が終わるまで待機させられます。これはSSRとまったく同じ動きです（これが*blocking*という名前が使われる理由です）。結果は将来のリクエストのためにキャッシュされるので、この待機が発生するのは1つのパスにつき1回だけです。

`getStaticProps`は次のように振る舞います：

- `getStaticPaths`が返したパスはビルド時に`getStaticProps`を利用してレンダリングされます。
- ビルド時に生成されなかったパスへのリクエストは404ページに導かれ**ません**。代わりに、Next.jsは当該のパスへの最初のリクエストがあった時点でSSRを行い、生成されたHTMLを返します。
- それが終わると、ブラウザは生成されたパスのHTMLを受け取ります。ユーザーの視点から見ると、"ブラウザがページをリクエストしている" 状態から "完全なページが読み込まれた" 状態への遷移となります。読み込み中ないしフォールバックの状態は一瞬たりとも発生しません。
- 同時に、Next.jsはこのパスを事前レンダリングされたページの一覧に加えます。同じパスに対する後続のリクエストは、ビルド時に事前レンダリングされたページに対するそれと同様に、生成済みページの配信を受けます。

デフォルトでは`fallback: 'blocking'` は生成されたページを *更新*しません。生成されたページを更新するには、`fallback: 'blocking'`と併せて[インクリメンタルな静的再生成](#incremental-static-regeneration)を利用する必要があります。

> [`next export`](/docs/advanced-features/static-html-export.md)を使用する場合、`fallback: 'blocking'`はサポートされません。

### `getStaticPaths`はどのような時に使うべきか？

`getStaticPaths`は動的ルートを持つページを静的に事前レンダリングする時に利用します。

### TypeScriptの場合： `GetStaticPaths`を使用する

TypeScriptで開発する場合、`next`モジュールが提供する`GetStaticPaths`型を利用できます：

```ts
import { GetStaticPaths } from 'next'

export const getStaticPaths: GetStaticPaths = async () => {
  // ...
}
```

### 技術的な詳細

#### `getStaticProps`とともに用いる

動的ルート・パラメーターを伴うページを`getStaticProps`で処理する場合、`getStaticPaths`を利用する必要があります。

`getServerSideProps`とともに`getStaticPaths`を利用することはできません。

#### ビルド時にサーバーサイドでのみ実行される

`getStaticPaths`はビルド時にサーバーサイドでのみ実行されます。

#### ページにおいてのみ許される

`getStaticPaths`は**page**からのみエクスポートできます。ページ以外のファイルからエクスポートすることはできません。

加えて、必ず`export async function getStaticPaths() {}`という形でエクスポートしなくてはなりません。当該ページのコンポーネントのプロパティとして`getStaticPaths`を定義しても動作**しません**。

#### 開発モードにおいてはリクエストの都度実行される

開発モード（`next dev`）においては、`getStaticPaths`はリクエストの都度実行されます。

## `getServerSideProps`（サーバーサイド・レンダリング）

<details>
  <summary><b>バージョン履歴</b></summary></details>

バージョン | 変更点
--- | ---
`v10.0.0` | `locale`、`locales`、`defaultLocale`、そして `notFound`オプションが追加された。
`v9.3.0` | `getServerSideProps`が導入された。



ページから`getServerSideProps`という名前の`async`関数をエクスポートした場合、Next.jsは当該ページを`getServerSideProps`が返したデータを用いてリクエストの都度事前レンダリングします。

```js
export async function getServerSideProps(context) {
  return {
    props: {}, // propsを通じてページ・コンポーネントに渡される
  }
}
```

`context`パラメーターは次のキーを含むオブジェクトです：

- `params`：動的ルートを利用するページの場合、`params` はルート・パラメーターを内包します。ページ名が`[id].js`ならば、`params`は`{ id: ... }`のようになります。より詳しく学ぶには[動的ルーティングのドキュメント](/docs/routing/dynamic-routes.md)をご覧ください。
- `req`：[HTTPリクエストを表すオブジェクト](https://nodejs.org/api/http.html#http_class_http_incomingmessage)で、[値をパースするための組み込みのヘルパー関数](#provided-req-middleware-in-getserversideprops)も提供します。
- `res`：[HTTPレスポンスを表すオブジェクト](https://nodejs.org/api/http.html#http_class_http_serverresponse)です。
- `query`：クエリ文字列を表すオブジェクトです。
- `preview`：当該ページがプレビュー・モードの時`preview`は`true`です。それ以外のときは`false`です。[プレビュー・モードのドキュメント](/docs/advanced-features/preview-mode.md)も参照のこと。
- `previewData`：`setPreviewData`により設定されたプレビュー・データです。[プレビュー・モードのドキュメント](/docs/advanced-features/preview-mode.md)も参照のこと。
- `resolvedUrl`：正規化されたバージョンのリクエストURLです。クライアント側の遷移のための`_next/data`プレフィクスが除去され、オリジナルのクエリ値を含んでいます。
- `locale`は現在のロケールを含みます<br>（[国際化ルーティング](/docs/advanced-features/i18n-routing.md)を有効にしている場合）。
- `locales`はサポートされているすべてのロケールを含みます（[国際化ルーティング](/docs/advanced-features/i18n-routing.md)を有効にしている場合）。
- `defaultLocale`はデフォルトのロケールとして設定されているものを含みます（[国際化ルーティングを有効にしている場合](/docs/advanced-features/i18n-routing.md)）。

`getServerSideProps`は次のもの伴うオブジェクトを返す必要があります：

- `props` ─ **任意の**オブジェクトで、ページ・コンポーネントが受け取るpropsです。[シリアライズ可能なオブジェクト](https://en.wikipedia.org/wiki/Serialization)かシリアライズ可能なオブジェクトに解決されるPromiseである必要があります。

- `notFound` ─ **任意の**真偽値で404ステータスとエラーページを返すことを可能にします。下記の例はこれがどのようにはたらくかを示すものです:

    ```js
    export async function getServerSideProps(context) {
      const res = await fetch(`https://...`)
      const data = await res.json()

      if (!data) {
        return {
          notFound: true,
        }
      }

      return {
        props: {}, // propsとしてページ・コンポーネントに渡される
      }
    }
    ```

- `redirect` ─ **任意の**パラメーターで、内的および外的なリソースに対するリダイレクトを可能にするものです。これは `{ destination: string, permanent: boolean }`のような形をとります。稀なケースですが、古いHTTPクライアントを正しくリダイレクトさせるために、独自のステータスを設定してやる必要があることがあります。このようなケースでは、`permanent`プロパティではなく`statusCode`プロパティが利用可能ですが、両方同時にはできません。下記のコードはこのパラメーターがどのようにはたらくか示すものです：

    ```js
    export async function getServerSideProps(context) {
      const res = await fetch(`https://.../data`)
      const data = await res.json()

      if (!data) {
        return {
          redirect: {
            destination: '/',
            permanent: false,
          },
        }
      }

      return {
        props: {}, // propsとしてページ・コンポーネントに渡される
      }
    }
    ```

> **注意**：`getServerSideProps`で使用するためにトップレベル・スコープにモジュールをインポートすることができます。<code>getServerSideProps</code>で使用されるそれらのモジュールはクライアントサイドのためのバンドルには含まれません。
>
> これはつまり**サーバーサイド・コードを`getServerSideProps`の中に直接に**記述できるということです。これには例えばファイルシステムないしデータベースからの読み込みを行うコードが含まれます。

> **注意**：[`fetch()`](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)を`getServerSideProps`の中でAPIルートを呼び出すのに利用してはいけません。そうではなくて、APIルートの内部で利用しているロジックを直接インポートして使います。このアプローチのために若干ですがリファクタリングが必要になるかもしれません。
>
> 外部APIからのデータ取得は問題ありません！

### `getServerSideProps`で提供される`req`ミドルウェア

`getServerSideProps`に渡されたコンテキストに含まれる`req`は組み込みのミドルウェアを提供します。このミドルウェアはリクエスト（req）の内容をパースするのに役立ちます。そのミドルウェアとは：

- `req.cookies` ─ リクエストにより送信されたクッキーを内包するオブジェクトです。デフォルトは `{}` です。

### 例

ここに示すのはリクエスト時にデータを取得するのに`getServerSideProps`を利用し事前レンダリングする例です。この例は[ページのドキュメント](/docs/basic-features/pages.md)でも出てきたものです。

```jsx
function Page({ data }) {
  // データをレンダリングする...
}

// この関数はリクエストの都度呼び出される
export async function getServerSideProps() {
  // 外部APIからデータを取得する
  const res = await fetch(`https://.../data`)
  const data = await res.json()

  // propsを通じてページにデータを渡す
  return { props: { data } }
}

export default Page
```

### `getServerSideProps`はどのような時に使うべきか？

リクエスト時にデータを取得する必要のあるページを事前レンダリングするためにのみ`getServerSideProps`を利用します。サーバーはリクエストの都度計算をしなくてはならなず、加えて追加の設定なしにCDNを使ったキャッシュをはたらかすこともできないため、最初の1バイトまでの時間（TTFB）は`getStaticProps`に劣ります。

データを事前レンダリングする必要がない場合は、データをクライアントサイドで取得することも検討しましょう。[より詳しく学ぶにはこちらをクリックしてください](#fetching-data-on-the-client-side)。

### TypeScriptの場合：`GetServerSideProps`を使用する

TypeScriptにより開発をする場合、`next`モジュールがエクスポートする`GetServerSideProps`型を利用できます：

```ts
import { GetServerSideProps } from 'next'

export const getServerSideProps: GetServerSideProps = async (context) => {
  // ...
}
```

ページが受け取るpropsに関して型推論の恩恵を享受したい場合、次に示すように`InferGetServerSidePropsType<typeof getServerSideProps>`を利用することができます：

```tsx
import { InferGetServerSidePropsType } from 'next'

type Data = { ... }

export const getServerSideProps = async () => {
  const res = await fetch('https://.../data')
  const data: Data = await res.json()

  return {
    props: {
      data,
    },
  }
}

function Page({ data }: InferGetServerSidePropsType<typeof getServerSideProps>) {
  // 投稿データはData型と推論される
}

export default Page
```

### 技術的な詳細

#### サーバーサイドでのみ実行される

`getServerSideProps`はサーバーサイドでのみ実行され、ブラウザ側では決して実行されません。あるページで`getServerSideProps`を利用する場合：

- 当該ページに直接リクエストが来た場合、`getServerSideProps`が実行され、この関数が返したpropsを元にページが事前レンダリングされます。
- 当該ページに対して`next/link`モジュール（[ドキュメント](/docs/api-reference/next/link.md)）や`next/router`モジュール（[ドキュメント](/docs/api-reference/next/router.md)）によるクライアントサイドのページ遷移が発生した場合、Next.jsはサーバー側にAPIリクエストを行い、`getServerSideProps`を実行します。`getServerSideProps`の実行結果を内包するJSONが返されると、これを用いてページのレンダリングが行われます。これらすべてがNext.jsにより自動で行われます。`getServerSideProps`を定義したら後は何もしなくてよいのです。

[こちらのツール](https://next-code-elimination.vercel.app/)を使うことで、<br> to verify what Next.jsがクライアントサイド・バンドルから問題のコードを除去していることを確認できます。

#### ページにおいてのみ許される

`getServerSideProps`は**page**からのみエクスポートできます。ページ以外のファイルからエクスポートすることはできません。

加えて、必ず`export async function getServerSideProps() {}`という形でエクスポートしなくてはなりません。当該ページのコンポーネントのプロパティとして`getServerSideProps`を定義しても動作**しません**。

## クライアントサイドでデータを取得する

頻繁に更新されるデータを含むページで、事前レンダリングが必要ない場合、データの取得をクライアントサイドで行うこともできます。この例の1つはユーザー固有のデータです。ここではそれがどのようにはたらくか見てみましょう：

- はじめに、データなしにページが表示されます。ページの部品は静的生成により事前レンダリングさせることもできます。欠けているデータについて読み込み中のステータスを表示することもできます。
- その後で、クライアントサイドでデータを取得し、準備ができたらこれを表示します。

このアプローチは、例えば、ユーザー・ダッシュボード・ページでうまく機能します。なぜなら、ダッシュボードはプライベートで、ユーザー固有のページで、SEOと無縁であり、事前レンダリングの必要がないからです。データは頻繁に更新されるので、リクエスト時にデータを取得する必要があります。

### SWR

Next.jsプロジェクトの背後にいるチームは、データ取得処理のために[**SWR**](https://swr.vercel.app/)という名前のReactフックを作成しました。クライアントサイドでデータ取得をする場合、このフックの利用を強くおすすめします。このフックはキャッシュ、再検証、フォーカスの追跡、一定間隔での再取得、その他の処理を実現します。このフックは次のようにして使うことができます：

```jsx
import useSWR from 'swr'

const fetcher = (url) => fetch(url).then((res) => res.json())

function Profile() {
  const { data, error } = useSWR('/api/user', fetcher)

  if (error) return <div>読み込みに失敗</div>
  if (!data) return <div>読み込み中...</div>
  return <div>こんにちは {data.name} さん！</div>
}
```

[より詳しく学ぶにはSWRのドキュメントをご確認ください](https://swr.vercel.app/)。

## 詳しく学ぶ

学習を進めるために次のセクションをご一読されることをおすすめいたします：

<div class="card">
  <a href="/docs/advanced-features/preview-mode.md">
    <b>プレビュー・モード：</b>
    <small>Next.jsにおけるプレビュー・モードについてさらに詳しく学びましょう。</small>
  </a>
</div>

<div class="card">
  <a href="/docs/routing/introduction.md">
    <b>ルーティング：</b>
    <small>Next.jsにおけるルーティングについてさらに詳しく学びましょう。</small>
  </a>
</div>

<div class="card">
  <a href="/docs/basic-features/typescript.md#pages">
    <b>TypeScript：</b>
    <small>ページのコードにTypeScriptを追加しましょう。</small>
  </a>
</div>
