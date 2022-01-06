---
description: Next.jsは、ImgixやCloudinaryその他のサードパーティ製イメージローダーと同様に、組み込みの画像最適化機能をサポートしています！　ここで詳しく学びましょう。
---

# imageコンポーネントと画像最適化

<details open>
  <summary><b>例</b></summary>
  <ul>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/image-component">imageコンポーネントの例</a></li>
  </ul></details>

Next.jsのimageコンポーネント──[`next/image`](/docs/api-reference/next/image.md)──はHTMLの`<img>`要素を拡張し、モダンWebのために進化させたものです。このコンポーネントは組み込みのパフォーマンス最適化のためのいろいろな機能を含んでおり、より良い[Core Web Vitals](https://nextjs.org/learn/seo/web-performance)を得る助けとなります。これらのスコアはあなたのWebサイトのユーザー体験の質を測るための重要な指標であり、[Googleの検索結果の表示順に影響を与え](https://nextjs.org/learn/seo/web-performance/seo-impact)ます。

imageコンポーネントに組み込まれた最適化には次のようなものが含まれます：

- **パフォーマンス改善：**いつでも各デバイスに最適なサイズの画像をモダンな画像フォーマットで配信します。
- **視覚的な安定性：**自動的に[Cumulative Layout Shift](https://nextjs.org/learn/seo/web-performance/cls)を防止します。
- **高速なページ読み込み：**画像はそれらがビューポートに入った段階で読み込まれれます。ぼかし画像をプレースホルダとして用いるオプションも提供されています。
- **アセットの柔軟性：**オンデマンドで画像のリサイズを行います。当該の画像がリモートのサーバーに保管されている場合でも機能します。

## imageコンポーネントを利用する

あなたのアプリケーションに画像を追加するには、[`next/image`](/docs/api-reference/next/image.md)コンポーネントをインポートします：

```jsx
import Image from 'next/image'
```

今度は、あなたの画像（ローカルもしくはリモートに保管されている）のための`src`を定義しましょう。

### ローカルの画像

ローカルの画像を使用するには、当該の`.jpg`、`.png`、あるいは`.webp`を`import`しましょう：

```jsx
import profilePic from '../public/me.png'
```

動的な`await import()`や`require()`はサポートしていません。 `import`は静的なものでなくてはならず、ビルド時に解析できる必要があります。

Next.jsはインポート対象のファイルに基づいて自動的に当該画像の`width`と`height`を決定します。これらの値は画像読み込み中の[Cumulative Layout Shift](https://nextjs.org/learn/seo/web-performance/cls)の防止のために利用されます。

```js
import Image from 'next/image'
import profilePic from '../public/me.png'

function Home() {
  return (
    <>
      <h1>私のホームページ</h1>
      <Image
        src={profilePic}
        alt="著者の画像"
        // width={500} は自動的に付与される
        // height={500} は自動的に付与される
        // blurDataURL="data:..." は自動的に付与される
        // placeholder="blur" // 読み込み中のぼかし画像表示も選択できる
      />
      <p>私のホームページにようこそ！</p>
    </>
  )
}
```

### リモートの画像

リモートの画像を使用するには、`src`プロパティにURL文字列を設定しなくてはなりません。URLは[相対的なもの](#loaders)でも[絶対的なもの](#domains)でも構いません。Next.jsはビルド時にそれらの画像にアクセスできないため、[`width`](/docs/api-reference/next/image.md#width)と[`height`](/docs/api-reference/next/image.md#height)、そして任意の項目である[`blurDataURL`](/docs/api-reference/next/image.md#blurdataurl)を手作業で指定してやる必要があります：

```jsx
import Image from 'next/image'

export default function Home() {
  return (
    <>
      <h1>私のホームページ</h1>
      <Image
        src="/me.png"
        alt="著者の画像"
        width={500}
        height={500}
      />
      <p>私のホームページにようこそ！</p>
    </>
  )
}
```

> `next/image`における[サイズ指定の要件](#image-sizing)でより詳しく学びましょう。

### ドメイン

リモートの画像にアクセスし、しかもNext.js組み込みの画像最適化APIを利用したい時もあるでしょう。そのような時は`loader`のデフォルトの設定状態のままにして、画像の絶対URLを`src`に入力します。

あなたのアプリケーションを悪意あるユーザーから守るため、あなたがこの方法でアクセスしたいドメインの一覧を定義する必要があります。この設定は下記のように`next.config.js`で行います：

```js
module.exports = {
  images: {
    domains: ['example.com', 'example2.com'],
  },
}
```

### ローダー

[先程の例](#remote-images)で、リモートの画像を参照するために部分URL（`"/me.png"`）が指定されています。これが可能なのは`next/image`の[ローダー](/docs/api-reference/next/image.md#loader)アーキテクチャのおかげです。

ローダーはあなたの画像を指すURLを生成する関数です。この関数はあなたが指定した`src`の値にルート・ドメインを付け加え、異なるサイズの画像をリクエストするための複数のURLを生成します。これらのURLは自動的[ソースセット](https://developer.mozilla.org/en-US/docs/Web/API/HTMLImageElement/srcset)生成で使用されます。これによりあなたのサイトの訪問者は各々のビューポートに合った正しいサイズの画像の配信を受けられます。

Next.jsアプリケーションのためのデフォルトのローダーは組み込みの画像最適化APIです。このローダーは画像がWeb上のどこにあろうと最適化を行い、Next.js Webサーバから直接配信します。画像をCDNや画像サーバから直接配信したい場合は、[組み込みのローダー](/docs/api-reference/next/image.md#built-in-loaders)のいずれかを使用するか、あなた自身でほんの数行のJavaScriptコードを記述します。

ローダーは画像単位で指定することも、アプリケーション単位で指定することも可能です。

### 優先順位

各ページの[Largest Contentful Paint (LCP) element](https://web.dev/lcp/#what-elements-are-considered)のために`priority`プロパティを指定すべきです。これによりNext.jsが読み込む画像の優先順位付け（例えばpreloadタグや優先順位ヒントによって）できるようになり、LCPの大きな改善に繋がります。

LCP要素は通常ページのビューポートに表示される大きな画像やテキスト・ブロックです。`next dev`を実行した時、LCP要素が`priority`プロパティ指定なしの`<Image>` であった場合、コンソールに警告が表示されます。

LCPに影響する画像が特定できたら、次のようにしてプロパティを追加します：

```jsx
import Image from 'next/image'

export default function Home() {
  return (
    <>
      <h1>私のホームページ</h1>
      <Image
        src="/me.png"
        alt="著者の画像"
        width={500}
        height={500}
        priority
      />
      <p>私のホームページにようこそ！</p>
    </>
  )
}
```

優先順位付けについてより詳しくは[`next/image`コンポーネントのドキュメント](/docs/api-reference/next/image.md#priority)をご覧ください。

## 画像のサイズ変更

画像によるパフォーマンス低下でもっとも一般的にみられるものは*レイアウト・シフト*です。これはページの読み込み過程で、画像が他の要素を押し出す現象です。このパフォーマンス上の問題はユーザーをとてもイライラさせるので、[Cumulative Layout Shift](https://web.dev/cls/)と呼ばれる固有のCore Web Vitalが定義されています。画像によるレイアウト・シフトを防ぐには、[必ず画像のサイズを指定](https://web.dev/optimize-cls/#images-without-dimensions)する必要があります。これによりブラウザは画像を読み込む前に画像のために必要な空白を正確に知ることができます。

`next/image`は良好なパフォーマンスを保証するよう設計されているので、レイアウト・シフトを助長するような使い方ができないようになっています。次に示す3つの方法のいずれかによって**必ず**サイズの指定をしてください：

1. [静的なimport](#local-images)により自動的にサイズ指定をさせる
2. `height` **および** `width` プロパティにより明示的にサイズ指定をする
3.  `layout="fill"`で画像をその親要素いっぱいに広がるように指定することにより、暗黙的にサイズ指定をする

> ### 画像のサイズが不明な場合どうなるか？
>
> 画像サイズの知識なしに画像にアクセスする場合でも、できることはいくつかあります：
>
> **`layout='fill'`を使う**
>
> `fill`レイアウト・モードは画像をその親要素いっぱいに表示します。CSSを使って画像の親要素にサイズ指定をすることを検討してみてください。指定したら、[`objectFit プロパティ`](/docs/api-reference/next/image.md#objectfit)に`fill`、`contain`、もしくは`cover`を指定するとともに、[`objectPosition プロパティ`](/docs/api-reference/next/image.md#objectposition)を用いて画像が親要素内の空間をどのように占めるかを定義します。
>
> **画像を正規化する**
>
> 画像の配信元があなたの管理下にある場合、画像処理経路を変更して画像を特定のサイズへと正規化することを検討してみてください。
>
> **API呼び出しを変更する**
>
> あなたのアプリケーションがAPI呼び出し（例えばCMSに対してするような）によって画像URLを取得している場合、API呼び出しを変更してURLだけでなく画像の寸法も返すようにすることもできます。

これまで紹介してきた画像サイズ変更の方法が活用できない場合、`next/image`コンポーネントは標準の`<img>`要素と協力して適切に機能するように設計されています。

## スタイルの付与

imageコンポーネントのスタイル付与は通常の`<img>`要素と変わるところがありませんが、いくつか心に留めておくべき指針があります：

**適切なレイアウト・モードを選ぶ**

imageコンポーネントはそれがページ内でどのようなサイズで表示されるか定義するいくつかの[レイアウト・モード](/docs/api-reference/next/image.md#layout)を持っています。imageコンポーネントのスタイルがあなたの望むものとならない場合は、他のレイアウト・モードを試してみてください。

**DOM構造ではなくclassNameで画像を特定する**

いずれのレイアウト・モードを使用する場合も、imageコンポーネントは常に1つの`<span>`タグに包まれた`<img>`タグというDOM構造を持ちます。いくつかのモードでは、空白を作るために兄弟要素の`<span>`タグを伴うこともあります。それらの付加的な`<span>`要素はレイアウト・シフトを防ぐために重大な役割を負っています。

内側の`<img>`タグにスタイル付与するためのおすすめの方法は、imageコンポーネントの`className`プロパティに[CSSモジュール](/docs/basic-features/built-in-css-support.md#adding-component-level-css)から読み込まれた値を指定するものです。`className`の値はコンポーネント内部の`<img>`要素に自動的に適用されます。

別の方法として、[global stylesheet](/docs/basic-features/built-in-css-support#adding-a-global-stylesheet)をインポートして、当該スタイルシートで使用されているのと同じクラス名を`className`プロパティに指定することもできます。

[styled-jsx](/docs/basic-features/built-in-css-support.md#css-in-js)はスコープが現在のコンポーネントに制限されるので、imageコンポーネントのスタイル指定には使用できません。

`<Image>`コンポーネントは`style`プロパティに設定された値をコンポーネント内部の`<img>`に渡さないので、これもスタイル指定には使用できません。

**`layout='fill'`を使用している場合、親要素は必ず`position: relative`でなくてはなりません。**

これは当該レイアウト・モードにおいてimage要素を適切にレンダリングするために不可欠です。

**`layout='responsive'`を使用している場合、親要素は必ず`display: block`でなくてはなりません。**

`<div>`要素ではこれがデフォルト値ですが、そうでない場合は明示的に指定する必要があります。

## プロパティ

[**`next/image`コンポーネントに指定できるすべてのプロパティをご確認ください。**](/docs/api-reference/next/image.md)

### スタイル付与の例

さまざまなfillモードでimageコンポーネントを用いる例については、[imageコンポーネント利用事例アプリ](https://image-component.nextjs.gallery/)をご覧ください。

## 設定変更

`next/image`コンポーネントとNext.jsの画像最適化APIは`next.config.js`ファイルで設定変更できます。これらの設定により[リモート・ドメインの有効化](/docs/api-reference/next/image.md#domains)、[独自の画像ブレイクポイントの定義](/docs/api-reference/next/image.md#device-sizes)、[キャッシュの挙動変更](/docs/api-reference/next/image.md#caching-behavior)その他を行うことができます。

[**詳しくは画像に関する設定のドキュメントを参照してください。**](/docs/api-reference/next/image.md#configuration-options)

## 関連情報

次にすべきことについての情報を知りたい方には、次のセクションをお読みになることをおすすめします：

<div class="card">
  <a href="/docs/api-reference/next/image.md">
    <b>next/image</b>
    <small>imageコンポーネントで利用可能なすべてのプロパティをご覧ください。</small>
  </a>
</div>
