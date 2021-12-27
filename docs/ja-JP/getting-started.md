---
description: この公式ドキュメントでNext.jsによる開発をはじめ、すべての機能について詳しく学びましょう。
---

# さあ、はじめましょう

Next.jsのドキュメントにようこそ！

Next.jsがはじめての方には [学習コース](https://nextjs.org/learn/basics/create-nextjs-app) をおすすめします。

このクイズを伴うインタラクティブな学習コースではNext.jsについて知る必要のあることを一通りご案内しています。

Next.jsに関する事項についてご質問がお有りでしたら、 [GitHub Discussions](https://github.com/vercel/next.js/discussions)上の私たちのコミュニティに遠慮なくお尋ねください。

#### システム要件

- [Node.js 12.22.0](https://nodejs.org/) もしくはそれ以降
- macOS、 Windows （WSLを含む）、そして Linux をサポート

## セットアップ

新しいNext.jsアプリを作成する場合は `create-next-app` のご利用をおすすめします。このコマンドはすべてを自動的にセットアップしてくれます。新しいプロジェクトを作成するために実行します：

```bash
npx create-next-app@latest
# もしくは
yarn create next-app
```

TypeScriptプロジェクトで開発をはじめたい方は `--typescript` フラグを利用できます：

```bash
npx create-next-app@latest --typescript
# もしくは
yarn create next-app --typescript
```

インストールが完了したら：

- <br>`npm run dev`もしくは`yarn dev` を実行することで、 `http://localhost:3000`で開発サーバーが起動します。
- `http://localhost:3000` にアクセスすればあなたのアプリケーションをご覧になれます。
- `pages/index.js`を編集するとその結果はブラウザ上で確認できます。

`create-next-app`の使用方法についてより詳しい情報をお望みでしたら、 <a data-md-type="raw_html" href="/docs/api-reference/create-next-app.md">`create-next-app`のドキュメント</a> でご覧になれます。

## 手作業でセットアップ

`next`、 `react`、そして`react-dom` をあなたのプロジェクトにインストールしましょう：

```bash
npm install next react react-dom
# もしくは
yarn add next react react-dom
```

`package.json` を開き、次の `scripts` を追加します：

```json
"scripts": {
  "dev": "next dev",
  "build": "next build",
  "start": "next start",
  "lint": "next lint"
}
```

これらのスクリプトはそれぞれアプロケーション開発の異なるステージに対応しています：

- `dev` - [`next dev`](/docs/api-reference/cli.md#development) を実行することで開発モードで Next.js を起動します。
- `build` - [`next build`](/docs/api-reference/cli.md#build)を実行することでプロダクション利用のためにアプリケーションをビルドします。
- `start` - [`next start`](/docs/api-reference/cli.md#production) を実行することでプロダクション・サーバーを起動します。
- `lint` - [`next lint`](/docs/api-reference/cli.md#lint) を実行することで Next.js組み込みのESLint構成をセットアップします。

Next.js は [pages](/docs/basic-features/pages.md) の概念を中心にして構築されています。1つのページは 1つの[Reactコンポーネント](https://reactjs.org/docs/components-and-props.html)であり、`pages`ディレクトリに収められた1つの `.js`、 `.jsx`、`.ts`ないし`.tsx`ファイル からエクスポートされます。

ページはファイル名に基づくルートに関連付けられます。例えば、 `pages/about.js` は`/about` にマッピングされます。動的ルートパラメーターをファイル名に加えることもできます。

プロジェクト内に`pages`ディレクトリーを作成しましょう。

次の内容で `./pages/index.js` を作成します：

```jsx
function HomePage() {
  return <div>Next.jsにようこそ！</div>
}

export default HomePage
```

現時点で、私たちは次のものを手にしています：

- 自動的なコンパイルとバンドル化（webpackとbabelにより行われる）
- [ReactのFast Refresh機能](https://nextjs.org/blog/next-9-4#fast-refresh)
- [`./pages/`](/docs/basic-features/pages.md)の[静的生成とサーバーサイド・レンダリング](/docs/basic-features/data-fetching.md)
- [静的ファイルの配信](/docs/basic-features/static-file-serving.md)。 `./public/` ディレクトリーは `/`にマッピングされます。

加えて、あらゆるNext.jsアプリケーションは最初からプロダクション・リリースのための準備ができています。詳しく知りたい方は[デプロイメントのドキュメント](/docs/deployment.md)をご覧ください。

## 関連事項

次にすべきことについての情報を知りたい方には、次のセクションをお読みになることをおすすめします：

<div class="card">
  <a href="/docs/basic-features/pages.md">
    <b>ページ：</b>
    <small>Next.jsのページについてより詳しく学びましょう。</small>
  </a>
</div>

<div class="card">
  <a href="/docs/basic-features/built-in-css-support.md">
    <b>CSSサポート：</b>
    <small>組み込みのCSSサポートを使用してあなたのアプリに独自のスタイルを追加しましょう。</small>
  </a>
</div>

<div class="card">
  <a href="/docs/api-reference/cli.md">
    <b>CLI：</b>
    <small>Next.js のCLIについてより詳しく学びましょう。</small>
  </a>
</div>
