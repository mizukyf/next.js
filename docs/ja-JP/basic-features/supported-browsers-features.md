---
description: ブラウザ・サポートと、Next.jsによりサポートされているJavaScript機能。
---

# サポートされているブラウザと機能

Next.js は**IE11とすべてのモダン・ブラウザ**（Edge、Firefox、Chrome、Safari、Opera、その他）をサポートしています。いかなる設定も不要です。

## ポリフィル

IE11の互換性のために必要なポリフィルは透過的に注入されます。これに加えて、広範に使用されるポリフィルも注入します。これには次のものが含まれます：

- [**fetch()**](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)の置き換え：`whatwg-fetch`と`unfetch`。
- [**URL**](https://developer.mozilla.org/en-US/docs/Web/API/URL)の置き換え：[`url` パッケージ（Node.js API）](https://nodejs.org/api/url.html)。
- [**Object.assign()**](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign)の置き換え：`object-assign`、`object.assign`、そして`core-js/object/assign`。

あなたのアプリの依存性にこれらのポリフィルが含まれている場合、プロダクション・ビルドではこれらの重複を防ぐために自動的に削除されます。

加えて、バンドル・サイズを抑えるため、Next.jsはそれらが実際に必要になった時にのみ読み込みます。WWWのトラフィックの多くは、これらのポリフィルをダウンロードせずに済みます。

### サーバーサイド・ポリフィル

クライアントサイドにおける`fetch()`に加えて、Next.jsはNode.js環境においても`fetch()`のポリフィルを提供します。開発者はサーバーサイドのコード（`getStaticProps`や`getServerSideProps`のような）の内部で`fetch()`を利用できるのです。`isomorphic-unfetch`や`node-fetch`のようなポリフィルは不要です。

### 独自のポリフィル

あなた自身のコードや何かしらの外部のnpm依存性が要求する機能をアプリ配信対象のブラウザがサポートしていない場合、あなた自身でポリフィルを追加する必要があります。

このような場合、 [独自の`<App>`](/docs/advanced-features/custom-app.md)もしくは個々のコンポーネントにおいて、**当該の polyfill**をトップレベル・インポートしてやる必要があります。

## JavaScript言語機能

Next.jsはデフォルトで開発者が最新のJavaScriptの機能を利用できるようにします。[ES6の機能](https://github.com/lukehoban/es6features)に加えて、Next.jsは次のものもサポートします：

- [Async/await](https://github.com/tc39/ecmascript-asyncawait)（ES2017）
- [オブジェクト・リテラルにおける展開構文](https://github.com/tc39/proposal-object-rest-spread)（ES2018）
- [動的`import()`](https://github.com/tc39/proposal-dynamic-import)（ES2020）
- [オプショナル・チェイニング](https://github.com/tc39/proposal-optional-chaining)（ES2020）
- [Null合体演算子](https://github.com/tc39/proposal-nullish-coalescing)（ES2020）
- [クラス・フィールド](https://github.com/tc39/proposal-class-fields)と[静的プロパティ](https://github.com/tc39/proposal-static-class-features)（第3ステージ提案の一部）
- その他！

### TypeScriptの機能

Next.jsは組み込みのTypeScriptサポートを提供します。[こちらでより詳しく学びましょう](/docs/basic-features/typescript.md)。

### Babelの設定をカスタマイズする（上級者向け）

Babelの設定をカスタマイズすることもできます。[こちらでより詳しく学びましょう](/docs/advanced-features/customizing-babel-config.md)。
