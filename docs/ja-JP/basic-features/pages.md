---
description: Next.jsページはpagesディレクトリー内のファイルからエクスポートされるReactコンポーネントです。ここではそれのページがどのようにはたらくかを学びます。
---

# ページ

> このドキュメントはNext.js バージョン9.3以上向けのものです。もしこれより古いバージョンをご利用の場合は [以前のドキュメント](https://nextjs.org/docs/tag/v9.2.2/basic-features/pages)をご覧ください。

Next.jsにおいて、1つの**ページ**は `pages`ディレクトリー内の1つの`.js`、`.jsx`、`.ts`もしくは`.tsx`ファイルからエクスポートされた1つの[React コンポーネント](https://reactjs.org/docs/components-and-props.html)です。それぞれのページはそれ自身のファイル名に基づくルートに関連付けられます。

**例**: 下記のようなReactコンポーネントをエクスポートする`pages/about.js`を作成すると、`/about`でアクセスできるようになります。

```jsx
function About() {
  return <div>About</div>
}

export default About
```

### 動的ルートを伴うページ

Next.jsは動的ルートを伴うページもサポートしています。例えば、<br>`pages/posts/[id].js`というファイルを作成すると、`posts/1`、`posts/2`といったパスでアクセスができるようになります。

> 動的ルーティングについてより詳しく学ばれたい方は、[動的ルーティングのドキュメント](/docs/routing/dynamic-routes.md)をご確認ください。

## 事前レンダリング

Next.jsはデフォルトですべてのページを**事前レンダリング**します。Next.jsはクライアントサイドJavaScriptによりすべてを行うのではなく、あらかじめ各ページのHTMLを生成するということです。事前レンダリングはよりよいパフォーマンスとSEOに貢献します。

生成された各ページのHTMLには、そのページのために必要不可欠な最小限のJavaScriptコードと関連付けられます。ブラウザによりページが読み込まれるとき、それらのJavaScriptコードが実行され、ページは完全にインタラクティブなものとなります。（このプロセスのことを*ハイドレーション*と呼びます。）

### 事前レンダリングの2つの形態

Next.jsの事前レンダリングには2つの形態があります：  **静的生成**と**サーバーサイド・レンダリング**です。2つの違いは各ページのためのHTMLが **いつ** 生成されるかにあります。

- [**静的生成（推奨）**](#static-generation-recommended)： HTMLは**ビルド時**に行われ、各ページ・リクエストの間で使い回されます。
- [**サーバーサイド・レンダリング**](#server-side-rendering)：HTMLは**リクエストの都度**生成されます。

重要な点ですが、Next.jsは各ページについて好ましいレンダリング形態をあなたが **選択**できるようにしています。<br>多くのページで静的生成を利用し、残りのページではサーバーサイド・レンダリングを利用する、"ハイブリッド" なNext.jsアプリを作成することができるのです。

パフォーマンス上の理由からサーバーサイド・レンダリングよりも**静的生成**を**おすすめ**します。静的に生成されたページはいかなる追加の設定もなしにCDNによるキャッシュを活用でき、パフォーマンス向上させることができます。しかしながら、いくつかのケースでは、サーバーサイド・レンダリングを選択せざるをえないこともあります。

もちろん静的生成やサーバーサイド・レンダリングとともに**クライアントサイド・レンダリング**を利用することも可能です。ページのいくつかのパーツをもっぱらクライアントサイドのJavaScriptによりレンダリングさせることもできるということです。より詳しく学ぶには、[データ・フェッチ](/docs/basic-features/data-fetching.md#fetching-data-on-the-client-side) のドキュメントを参照してください。

## 静的生成（推奨）

<details open>
  <summary><b>例</b></summary>
  <ul>
    <li>
<a href="https://github.com/vercel/next.js/tree/canary/examples/cms-wordpress">WordPress の例</a>（<a href="https://next-blog-wordpress.vercel.app">デモ</a>）</li>
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
    <li><a href="https://static-tweet.vercel.app/">Static Tweet（Demo）</a></li>
  </ul></details>

**静的生成**を利用したページのHTMLは**ビルド時**に生成されます。プロダクション・モードにおいては、ページのHTMLは`next build`を実行した時に生成されるということです。このHTMLは各リクエストへの応答で使い回されます。これによりCDNによるキャッシュが可能になります。

Next.jsでは、**データを伴う、もしくは、データを伴わない**静的なページ生成が可能です。それでは、それぞれのケースについて見てみましょう。

### データを伴わない静的生成

Next.jsはデフォルトでデータ・フェッチを伴わない静的生成を用いて事前レンダリングを行います。ここに示すのはその例です：

```jsx
function About() {
  return <div>About</div>
}

export default About
```

このページは事前レンダリングのためにいかなる外部データも必要としない点に注目してください。このようなケースでは、Next.jsはビルド時にページごとに1つのHTMLファイルを生成します。

### データを伴う静的生成

事前レンダリングのために外部データの取得が必要になることもあります。2つのシナリオがありますが、どちらか片方ないし両方ともを適用可能です。それぞれのケースのために、Next.jsが提供する関数を利用することができます：

1. ページの**内容**が外部データに依存する場合：`getStaticProps`関数を利用します。
2. ページの**パス**が外部データに依存する場合：`getStaticPaths`関数（これに加えてしばしば`getStaticProps`関数も）を利用します。

#### シナリオ1：そのページの**内容**が外的データに依存する場合

**例**：あなたのブログ・ページがCMS（コンテンツ・マネジメント・システム）からブログ投稿の一覧を取得する必要がある場合。

```jsx
// TODO: `posts`を取得する必要あり（何かしらのAPIエンドポイントを呼び出すことで）
//       このページが事前レンダリングされるそのまえに。
function Blog({ posts }) {
  return (
    <ul>
      {posts.map((post) => (
        <li>{post.title}</li>
      ))}
    </ul>
  )
}

export default Blog
```

ページと同じファイルから`getStaticProps`という名前の `async`関数を`エクスポート`することで、事前レンダリング時にこのデータを取得することが可能になります。この関数はビルド時に呼び出され、取得したデータは事前レンダリングされるページの`props`へと渡されます。

```jsx
function Blog({ posts }) {
  // 投稿データをレンダリングする...
}

// この関数はビルド時に呼び出される
export async function getStaticProps() {
  // 投稿データを取得するために外部APIのエンドポイントを呼び出す
  const res = await fetch('https://.../posts')
  const posts = await res.json()

  // この関数が { props: { posts } } を返すことで、Blogコンポーネントは
  // ビルド時に `posts` をpropsの1項目として受け取ることができる
  return {
    props: {
      posts,
    },
  }
}

export default Blog
```

`getStaticProps`がどのようにはたらくかをより詳しく学ぶには[データ・フェッチのドキュメント](/docs/basic-features/data-fetching.md#getstaticprops-static-generation)をご確認ください。

#### シナリオ2：ページのパスが外的データに依存する場合

Next.jsでは**動的ルート**を伴うページを作成することもできます。例えば、`pages/posts/[id].js`という名前のファイルを作成することで、`id`の値に基づいて1つのブログ記事を表示することができます。これにより`posts/1`というパスでアクセスがあった時に`id: 1`のブログ記事を表示するということが可能になります。

> 動的ルーティングについてより詳しく学ぶには[動的ルーティングのドキュメント](/docs/routing/dynamic-routes.md)をご覧ください。

しかしながら、ビルド時の事前レンダリングに際して、`id`がいかなる値となるかは外部データに依存します。

**例**：データベースにただ1件だけブログ投稿（`id: 1`）を追加していたと仮定しましょう。この時点では、ビルド時に`posts/1`だけを事前レンダリングすればよいでしょう。

後日、2つめの記事を`id: 2`で追加したとしましょう。すると、`posts/2`も同じように事前レンダリングする必要がでてくるはずです。

つまり事前レンダリングの対象となるページの**paths**が外部データに依存しているわけです。動的ページ（今回の例では`pages/posts/[id].js` ）のファイルから`getStaticPaths`という名前の`async`関数を`エクスポート`することで、この状況を解決することができます。この関数はビルド時に呼び出され、どのパスを事前レンダリングすべきかを指示します。

```jsx
// この関数はビルド時に呼び出される
export async function getStaticPaths() {
  // 投稿データを取得するため外部APIのエンドポイントを呼び出す
  const res = await fetch('https://.../posts')
  const posts = await res.json()

  // 投稿データに基づき事前レンダリングすべきパスを得る
  const paths = posts.map((post) => ({
    params: { id: post.id },
  }))

  // ビルド時にこれらのパスだけが事前レンダリングされる。
  // { fallback: false } はその他のパスへのアクセスを404とすべきという意味。
  return { paths, fallback: false }
}
```

`pages/posts/[id].js`のなかでもまた、`getStaticProps`をエクスポートする必要があります。これにより`id`に基づき投稿データを取得し、ページの事前レンダリングに利用することができるようになります：

```jsx
function Post({ post }) {
  // 投稿をレンダリングする...
}

export async function getStaticPaths() {
  // ...
}

// この関数もまたビルド時に呼び出される
export async function getStaticProps({ params }) {
  // paramsは投稿の `id` を含んでいる。
  // もしルートが /posts/1 ならば params.id は 1
  const res = await fetch(`https://.../posts/${params.id}`)
  const post = await res.json()

  // propsを通じて投稿データをページに渡す
  return { props: { post } }
}

export default Post
```

`getStaticPaths`がどのようにはたらくか、より詳しく学ぶには[データ・フェッチのドキュメント](/docs/basic-features/data-fetching.md#getstaticpaths-static-generation)をご覧ください。

### 静的生成はいつ利用すべきなのか？

それが可能な時にはいつでも**静的生成**（データを伴うものであれ伴わないものであれ）を利用することをおすすめします。なぜならば、ページが1回限り組み立てられ、CDNにより配信されることで、リクエスト毎にサーバーサイド・レンダリングするより高速に機能するからです。

多くのタイプのページに静的生成が利用可能です。これには次のようなものが含まれます：

- マーケティング・ページ
- ブログやポートフォリオサイト
- ECサイトの製品リスト
- ヘルプやドキュメント

自問してみてください："ユーザーのリクエストに**先んじて**このページをレンダリングすることが可能だろうか？" もし答えがYESなのであれば、静的生成を選ぶべきです。

一方で、もしユーザーのリクエストに先んじて当該のページを事前レンダリングできないのであれば、静的生成は好ましく**ない**選択肢です。データ更新が頻繁に発生するページや、リクエストの都度コンテンツが変化するページなどがこれです。

このようなケースでは、次のうちどれかを行うことができます：

- **Client-side Rendering**とともに静的生成を用いる：ページのいくつかのパーツについて事前レンダリングをスキップし、それら表示するのにクライアントサイドJavaScriptを用います。このアプローチについてより詳しく学ぶには[データ・フェッチのドキュメント](/docs/basic-features/data-fetching.md#fetching-data-on-the-client-side)をご覧ください。
- **サーバーサイド・レンダリング**を用いる：Next.jsはリクエストの都度ページを事前レンダリングします。ページはCDNによりキャッシュされないので、応答は遅くなります。しかし事前レンダリングされるページのコンテンツはいつでも最新に保たれます。このアプローチについてこのあと解説をします。

## サーバーサイド・レンダリング

> この方式は "SSR" ないし "動的レンダリング" とも呼ばれます。

ページが**サーバーサイド・レンダリング**を用いる場合、当該ページのHTMLは**リクエストの都度**生成されます。

あるページに対してサーバーサイド・レンダリングを用いるには、`getServerSideProps`という名前の `async`関数を`エクスポート`する必要があります。この関数はリクエストの都度サーバー側で呼び出されます。

例えば、頻繁に更新されるデータ（外部APIから取得される）を事前レンダリングしたいページがあると仮定します。このデータを取得し、`Page`に渡すために下記のような`getServerSideProps`関数を記述します：

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

ご覧のように、`getServerSideProps`は`getStaticProps`に似通ったところがありますが、`getServerSideProps`はビルド時ではなくリクエストの都度実行される点で異なります。

`getServerSideProps`がどのようにはたらくか、より詳しく学ぶには[データ・フェッチのドキュメント](/docs/basic-features/data-fetching.md#getserversideprops-server-side-rendering)をご覧ください。

## まとめ

ここまでNext.jsが提供する2つの事前レンダリング形態について議論してきました。

- **静的生成（推奨）**：HTMLは**ビルド時**に生成され、毎回のリクエストで再利用されます。静的生成を利用するには、ページ・コンポーネントをエクスポートするか、`getStaticProps`（必要に応じて`getStaticPaths`も）をエクスポートするかします。ユーザーのリクエストに先んじて事前レンダリングができるページには素晴らしい効果があります。付加的なデータをユーザーに届けるためにクライアントサイド・レンダリングを併用することもできます。
- **サーバーサイド・レンダリング**：HTMLは**リクエストの都度**生成されます。サーバーサイド・レンダリングを利用するには、`getServerSideProps`をエクスポートします。サーバーサイド・レンダリングは静的生成に比べるとパフォーマンスで劣るので、どうしても必要な時だけこの方法を利用します。

## 詳しく学ぶ

学習を進めるために次のセクションをご一読されることをおすすめいたします：

<div class="card">
  <a href="/docs/basic-features/data-fetching.md">
    <b>データ・フェッチ：</b>
    <small>Next.jsにおけるデータ取得についてさらに詳しく学びます。</small>
  </a>
</div>

<div class="card">
  <a href="/docs/advanced-features/preview-mode.md">
    <b>プレビュー・モード：</b>
    <small>Next.jsにおけるプレビュー・モードについてさらに詳しく学びます。</small>
  </a>
</div>

<div class="card">
  <a href="/docs/routing/introduction.md">
    <b>ルーティング：</b>
    <small>Next.jsにおけるルーティングについてさらに詳しく学びます。</small>
  </a>
</div>

<div class="card">
  <a href="/docs/basic-features/typescript.md#pages">
    <b>TypeScript：</b>
    <small>ページのコードにTypeScriptを追加します。</small>
  </a>
</div>
