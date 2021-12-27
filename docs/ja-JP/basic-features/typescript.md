---
description: Next.jsはデフォルトでTypeScriptでサポートします。そこにはページを作成するための組み込み型やAPIが含まれます。ここではTypeScriptを用いてNext.jsをはじめる方法を学びましょう。
---

# TypeScript

<details>
  <summary><b>例</b></summary>
  <ul>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/with-typescript">TypeScript</a></li>
  </ul></details>

Next.jsはまるでIDEのように統合された[TypeScript](https://www.typescriptlang.org/)体験を追加設定なしに提供します。

## `create-next-app`によるサポート

`--ts, --typescript`フラグとともに[`create-next-app`](https://nextjs.org/docs/api-reference/create-next-app)を利用することでTypeScriptプロジェクトを作成できます。

```
npx create-next-app@latest --ts
# もしくは
yarn create next-app --typescript
```

## 既存プロジェクトの場合

既存プロジェクトにTypeScriptを導入するにはまず、空の`tsconfig.json`ファイルをプロジェクトのルート・フォルダーに作成します：

```bash
touch tsconfig.json
```

Next.jsはデフォルト値を用いてこのファイルを自動で構成します。あなた自身で`tsconfig.json`を用意し、独自の[コンパイラー・オプション](https://www.typescriptlang.org/docs/handbook/compiler-options.html)を指定することもサポートされています。

`next.config.js`ファイルの中に`typescript.tsconfigPath`プロパティ追加することで、 tsconfig.jsonファイルを相対パス指定することもできます。

> Next.jsはTypeScriptを処理するのにBabelを利用します。これに伴っていくつかの[注意事項](https://babeljs.io/docs/en/babel-plugin-transform-typescript#caveats)があるのと、いくつかの[コンパイラー・オプションが異なった形で処理されます](https://babeljs.io/docs/en/babel-plugin-transform-typescript#typescript-compiler-options)。

続いて、`next`を実行します（一般的には`npm run dev`もしくは`yarn dev`）。すると、Next.jsはセットアップを完了させるために必要なパッケージのインストールをあなたに案内してくれます：

```bash
npm run dev

# You'll see instructions like these:
#
# Please install TypeScript, @types/react, and @types/node by running:
#
#         yarn add --dev typescript @types/react @types/node
#
# ...
```

これで`.js`を`.tsx`に変換し、TypeScriptの恩恵を享受するための準備ができました！

> `next-env.d.ts`という名前のファイルがあなたのプロジェクトのルートに作成されているでしょう。 このファイルはTypeScriptコンパイラがNext.jsの組み込み型を確実に捕捉できるようにするものです。<br>いついかなる時も**このファイルを削除したり編集したりしてはいけません**。

> TypeScriptの`strict`モードはデフォルトでOFFにされています。TypeScriptによる開発に自信がお有りの場合は、`tsconfig.json`の中でこの項目をONにすることをおすすめします。

> `next-env.d.ts`を編集する代わりに、別のファイル──例えば`additional.d.ts`のような──を作成し、それを`tsconfig.json`内の`include`配列から参照することで、追加の型情報をプロジェクトに包含することができます。

デフォルトで、`next build`の工程の1つとして、型チェックが実行されます。コーディング過程でも型チェックを行うコード・エディタを使用されることをおすすめします。

エラー・レポートを黙らせたいという場合は、[TypeScriptエラーを無視する](/docs/api-reference/next.config.js/ignoring-typescript-errors.md)ためのドキュメントを参照してください。

## 静的生成とサーバーサイド・レンダリング

`getStaticProps`、`getStaticPaths`、そして`getServerSideProps`に関して、それぞれ対応する型`GetStaticProps`、`GetStaticPaths`、そして`GetServerSideProps`が利用できます：

```ts
import { GetStaticProps, GetStaticPaths, GetServerSideProps } from 'next'

export const getStaticProps: GetStaticProps = async (context) => {
  // ...
}

export const getStaticPaths: GetStaticPaths = async () => {
  // ...
}

export const getServerSideProps: GetServerSideProps = async (context) => {
  // ...
}
```

> `getInitialProps`を利用する場合は、[このページの指示に従う](/docs/api-reference/data-fetching/getInitialProps.md#typescript)こともできます。

## APIルート

次のコードはAPIルートのための組み込み型の使い方を示す例です：

```ts
import type { NextApiRequest, NextApiResponse } from 'next'

export default (req: NextApiRequest, res: NextApiResponse) => {
  res.status(200).json({ name: '山田 太郎' })
}
```

レスポンス・データの型を示すこともできます：

```ts
import type { NextApiRequest, NextApiResponse } from 'next'

type Data = {
  name: string
}

export default (req: NextApiRequest, res: NextApiResponse<Data>) => {
  res.status(200).json({ name: '山田 太郎' })
}
```

## 独自の`App`

[独自の`App`](/docs/advanced-features/custom-app.md)を利用している場合、組み込み型`AppProps`を使い、かつ、ファイル名を `./pages/_app.tsx`へと変更することができます：

```ts
// import App from "next/app";
import type { AppProps /*, AppContext */ } from 'next/app'

function MyApp({ Component, pageProps }: AppProps) {
  return <Component {...pageProps} />
}

// アプリケーションの各ページでブロッキング・データの要件がある場合のみ
// このメソッドをコメント解除する。これにより自動静的最適化が無効化され、
// すべてのページがサーバーサイド・レンダリングされる
//
// MyApp.getInitialProps = async (appContext: AppContext) => {
//   // 各ページの `getInitialProps` を呼び出し、 `appProps.pageProps` に値を詰め込む
//   const appProps = await App.getInitialProps(appContext);

//   return { ...appProps }
// }

export default MyApp
```

## パスのエイリアスとbaseUrl

Next.jsは`tsconfig.json` の`"paths"`と`"baseUrl"`オプションを自動的にサポートします。

この機能については[モジュール・パス・エイリアスのドキュメント](/docs/advanced-features/module-path-aliases.md)でより詳しく学ぶことができます。

## next.config.jsの型チェック

`next.config.js`ファイルはJavaScriptと決まっているので、BabelないしTypeScriptによる処理対象になりません。しかしながら、下記のようにJSDocを利用することで、IDEにいくつかの型チェックを行ってもらうことができます：

```js
// @ts-check

/**
 * @type {import('next').NextConfig}
 **/
const nextConfig = {
  /* ここに設定オプション */
}

module.exports = nextConfig
```

## インクリメンタルな型チェック

バージョン`v10.2.1`以降、Next.jsは[インクリメンタルな型チェック](https://www.typescriptlang.org/tsconfig#incremental)をサポートしています。`tsconfig.json`でこの機能がONになっている場合、大規模なアプリケーションにおいて型チェックの速度向上が得られる可能性があります。

この機能を利用する際は[最高のパフォーマンス](https://devblogs.microsoft.com/typescript/announcing-typescript-4-3/#lazier-incremental)を得るために、少なくともバージョン`v4.3.2`以上のTypeScriptの利用を強くおすすめします。
