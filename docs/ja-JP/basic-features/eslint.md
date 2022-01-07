---
description: Next.jsは統合されたESLint体験をデフォルトで提供します。 その適合性のルールはあなたがNext.jsを最適な方法で利用するのを手助けします。
---

# ESLint

バージョン**11.0.0**以降、Next.jsは統合された[ESLint](https://eslint.org/)体験をデフォルトで提供するようになりました。`package.json`に対してスクリプトとして`next lint`を追加しましょう：

```json
"scripts": {
  "lint": "next lint"
}
```

そして、`npm run lint`もしくは`yarn lint`を実行しましょう：

```bash
yarn lint
```

あなたのアプリケーションにESLintの設定が済んでいない場合、インストールと設定のプロセスが案内されます。

```bash
yarn lint

# 次のようなプロンプトが表示されます：
#
# ? How would you like to configure ESLint?
#
# ❯   Base configuration + Core Web Vitals rule-set (recommended)
#     Base configuration
#     None
```

次の3つの選択肢から1つを選ぶことができます：

- **Strict**：厳格な[Core Web Vitals ルールセット](/docs/basic-features/eslint.md#core-web-vitals)を伴うNext.jsベースのESLint設定を読み込む。はじめてESLintをセットアップする開発者向けの推奨設定です。

    ```json
    {
      "extends": "next/core-web-vitals"
    }
    ```

- **Base**：Next.jsベースのESLint設定を読み込む。

    ```json
    {
      "extends": "next"
    }
    ```

- **Cancel**：いかなるESLint設定も読み込まない。あなたが独自のESLint設定を行おうとしているときのみこのオプションを選びましょう。

ESLintの設定を読み込む2つの選択肢からいずれかを選んだ場合、Next.jsは開発時の依存性として`eslint`と`eslint-config-next`を自動的にインストールし、選択した設定を内容とする`.eslintrc.json`ファイルをプロジェクトのルートに配置します。

これでESLintによるチェックを行いたい時はいつでも`next lint`でそれを実行することができるようになりました。ひとたびESLintが設定されると、ビルド（`next build`）の都度それが実行されるようになります。もしエラーがあればビルドは失敗します。警告の場合はそうなりません。

> `next build`に際してESLintを実行させたくない場合は、[ESLintを無視する](/docs/api-reference/next.config.js/ignoring-eslint.md)ドキュメントをご覧ください。

適切な [統合](https://eslint.org/docs/user-guide/integrations#editors)の機能を用いて開発中にあなたのコード・エディタで直接エラーや警告をご覧になることができるようにすることをおすすめいたします。

## ESLintの設定

デフォルトの設定（`eslint-config-next`）はNext.jsにおいて最適なデフォルトのチェックを受けるのに必要なすべてを含んでいます。あなたのアプリケーションにおいてESLintの設定がお済みでない場合は、`next lint`コマンドを使用してデフォルトの設定とともにESLintを導入されることをおすすめいたします。

> 他のESLint設定とともに`eslint-config-next`を利用したい場合、いかなる競合も起こさずそれを行う方法を学ぶために[追加の設定項目](/docs/basic-features/eslint.md#additional-configurations)のセクションをご覧ください。

次のESLintプラグインから提供されている推奨のルールセットは、すべて`eslint-config-next`に含まれています：

- [`eslint-plugin-react`](https://www.npmjs.com/package/eslint-plugin-react)
- [`eslint-plugin-react-hooks`](https://www.npmjs.com/package/eslint-plugin-react-hooks)
- [`eslint-plugin-next`](https://www.npmjs.com/package/@next/eslint-plugin-next)

[`eslint-config-next`](https://www.npmjs.com/package/eslint-config-next)パッケージのドキュメントで、共通設定に関する完全な詳細をご覧いただくことができます。

これらは`next.config.js`の設定よりも優先されます。

## ESLintプラグイン

Next.jsはESLintプラグイン、[`eslint-plugin-next`](https://www.npmjs.com/package/@next/eslint-plugin-next)を提供しており、Next.jsアプリケーションにおける一般的な問題を検知するのを可能にするための設定にはじめから組み込まれています。ルールの全量は次のとおりです：

|  | ルール | 説明 |
| :-: | --- | --- |
| ✔️ | [next/google-font-display](https://nextjs.org/docs/messages/google-font-display) | Google Fontsとともにoptionalもしくはswapのfont-display記述子の挙動を強制する。 |
| ✔️ | [next/google-font-preconnect](https://nextjs.org/docs/messages/google-font-preconnect) | Google Fontsとともにpreconnectを利用することを強制する。 |
| ✔️ | [next/link-passhref](https://nextjs.org/docs/messages/link-passhref) | 独自のLinkコンポーネントとともにpassHrefプロパティを利用することを強制する。 |
| ✔️ | [next/no-css-tags](https://nextjs.org/docs/messages/no-css-tags) | 手書きのスタイルシート・タグを防止する。 |
| ✔️ | [next/no-document-import-in-page](https://nextjs.org/docs/messages/no-document-import-in-page) | next/documentをpages/document.jsの外部でインポートすることをできなくする。 |
| ✔️ | [next/no-head-import-in-document](https://nextjs.org/docs/messages/no-head-import-in-document) | pages/document.jsでnext/headをインポートすることをできなくする。 |
| ✔️ | [next/no-html-link-for-pages](https://nextjs.org/docs/messages/no-html-link-for-pages) | ページへのHTMLアンカー・リンクをLinkコンポーネントを用いずに作成することを禁じる。 |
| ✔️ | [next/no-img-element](https://nextjs.org/docs/messages/no-img-element) | HTML標準の&lt;img&gt;要素の利用を禁じる。 |
| ✔️ | [next/no-page-custom-font](https://nextjs.org/docs/messages/no-page-custom-font) | 特定ページ独自のfontsを防止する。 |
| ✔️ | [next/no-sync-scripts](https://nextjs.org/docs/messages/no-sync-scripts) | 同期的スクリプトを禁じる。 |
| ✔️ | [next/no-title-in-document-head](https://nextjs.org/docs/messages/no-title-in-document-head) | next/documentでHeadとともに&lt;title&gt;を用いることをできなくする。 |
| ✔️ | [next/no-unwanted-polyfillio](https://nextjs.org/docs/messages/no-unwanted-polyfillio) | Polyfill.ioから提供されるポリフィルが重複することを防止する。 |
| ✔️ | [next/inline-script-id](https://nextjs.org/docs/messages/inline-script-id) | インライン・コンテンツを伴ういnext/scriptコンポーネントのID属性を強制する。 |
| ✔️ | next/no-typos | <br>[Next.jsのデータ取得関数](https://nextjs.org/docs/basic-features/data-fetching)の宣言でタイプミスがないことを確実にする。 |
| ✔️ | [next/next-script-for-ga](https://nextjs.org/docs/messages/next-script-for-ga) | 必要なスクリプトを遅延読み込みするためScriptコンポーネントを使用する。 |

- ✔：推奨設定で有効化される。

あなたのアプリでESLintがすでに設定済みの場合、いくつかの条件に該当する時以外は、`eslint-config-next`を読み込むのではなく、このプラグインを直接拡張することをおすすめします。より詳しく学ぶには[推奨プラグイン・ルールセット](/docs/basic-features/eslint.md#recommended-plugin-ruleset)を参照してください。

### 独自の設定

#### `rootDir`

Next.jsがインストールされていないプロジェクトのルート・ディレクトリ（monorepoのような）で`eslint-plugin-next` を利用する場合、`eslint-plugin-next`がどこに配置されているかをNext.jsアプリケーションに示してやることができます。これには`.eslintrc`の`settings`プロパティを利用します：

```json
{
  "extends": "next",
  "settings": {
    "next": {
      "rootDir": "/packages/my-app/"
    }
  }
}
```

`rootDir`にはパス（相対ないし絶対）、glob（例えば`"/packages/*/"`のような）、それにパスおよび（ないし）globの配列を指定することができます。

## 独自のディレクトリとファイルをチェックする

デフォルトでは、Next.jsは`pages/`、`components/`、そして`lib/`ディレクトリの中のすべてのファイルについてESLintによるチェックを行います。しかしながら、プロダクション・ビルドのために`next.config.js`の `eslint`設定で`dirs`オプションを利用することでディレクトリを指定することも可能です：

```js
module.exports = {
  eslint: {
    dirs: ['pages', 'utils'], // プロダクション・ビルド（next build）において'pages' と 'utils' ディレクトリのみチェックする
  },
}
```

同様に、 `--dir`と `--file`フラグを使うことで、`next lint`に特定のディレクトリやファイルをチェックさせることができます：

```bash
next lint --dir pages --dir utils --file bar.js
```

## キャッシュ

パフォーマンスを向上させるため、ESLintにより処理されるファイルの情報はデフォルトでキャッシュされます。それらは`.next/cache`もしくはあなたの定義した[ビルド・ディレクトリ](/docs/api-reference/next.config.js/setting-a-custom-build-directory)に保存されます。単一ソース・ファイルのコンテンツより大きな情報に依存するESLintルールを追加し、キャッシュを無効化する必要がある場合、`next lint`に`--no-cache`を付けて利用します。

```bash
next lint --no-cache
```

## ルールを無効化する

サポートされているプラグイン（`react`、`react-hooks`、`next`）が提供するいずれかのルールを変更したり無効化したりする場合、`.eslintrc`の`rules`プロパティを使用して直接変更することができます：

```json
{
  "extends": "next",
  "rules": {
    "react/no-unescaped-entities": "off",
    "@next/next/no-page-custom-font": "off"
  }
}
```

### Core Web Vitals

はじめに`next lint`を実行して**strict**の選択肢を選んでいた場合、`next/core-web-vitals`ルールセットが有効になります。

```json
{
  "extends": "next/core-web-vitals"
}
```

`next/core-web-vitals`は`eslint-plugin-next`を更新し、[Core Web Vitals](https://web.dev/vitals/)に影響する項目でデフォルトでは警告扱いの多くのルールをエラー扱いにします。

> `next/core-web-vitals`は[Create Next App](/docs/api-reference/create-next-app.md)により構築された新しいアプリケーションに自動的に含められています。

## 他ツールとともに利用する

### Prettier

ESLintはコード・フォーマット・ルールも含んでおり、[Prettier](https://prettier.io/)を利用している場合それと競合することがあります。ESLintの設定ファイルにおいて[eslint-config-prettier](https://github.com/prettier/eslint-config-prettier)を読み込むことで、ESLintとPrettierとが協働するようにできます。

まず、依存性をインストールしましょう：

```bash
npm install --save-dev eslint-config-prettier
# もしくは
yarn add --dev eslint-config-prettier
```

続いて、既存のESLint設定に`prettier`を追加します：

```json
{
  "extends": ["next", "prettier"]
}
```

### lint-staged

If you would like to use `next lint` with <br><br>Gitのステージに置かれたファイルに対してチェックを行うため[lint-staged](https://github.com/okonet/lint-staged)とともに`next lint`を利用したい場合、プロジェクトのルートに次の内容の`.lintstagedrc.js`ファイルを配置します。これにより`--file`フラグ利用を指定します。

```js
module.exports = {
  '**/*.js?(x)': (filenames) =>
    `next lint --fix --file ${filenames
      .map((file) => file.split(process.cwd())[1])
      .join(' --file ')}`,
}
```

## 既存の設定を移行する

### 推奨プラグインのルールセット

すでにあなたのアプリケーションでESLintが設定済みで、次のうちいずれかの条件に該当する場合：

- 次のプラグインのうち1つないしそれ以上をすでにインストール済みである（個別に、あるいは、`airbnb`や`react-app`のような別の設定の仕組みを通じて）：
    - `react`
    - `react-hooks`
    - `jsx-a11y`
    - `import`
- Next.jsに組み込まれたBabelが設定されているのと異なる内容で特定の`parserOptions`を定義している（このような構成は[独自のBabel設定](/docs/advanced-features/customizing-babel-config.md)を利用しているのでない限り非推奨）
- インポートを処理するため定義されたNode.jsおよび（もしくは）TypeScriptの[リゾルバ](https://github.com/benmosher/eslint-plugin-import#resolvers)とともにインストールされた`eslint-plugin-import`を利用している

[`eslint-config-next`](https://github.com/vercel/next.js/blob/canary/packages/eslint-config-next/index.js)により設定するのを好むのならこれらを削除することを、さもなくばNext.jsのESLintプラグインを直接拡張することをおすすめいたします。

```js
module.exports = {
  extends: [
    //...
    'plugin:@next/next/recommended',
  ],
}
```

当該プラグインは`next lint`を実行するのでなしに通常の方法でインストールすることも可能です：

```bash
npm install --save-dev @next/eslint-plugin-next
# もしくは
yarn add --dev @next/eslint-plugin-next
```

これにより複数の設定作業により同じプラグインやパーサーが読み込まれて競合やエラーが発生するリスクが除去されます。

### 追加の設定項目

すでに独立したESLint設定を利用していて、そこに`eslint-config-next`を読み込みたい場合は、必ずそれが他の設定よりも後、一番最後に拡張されるようにしてください：

```
{
  "extends": ["eslint:recommended", "next"]
}
```

`next`の設定はすでに`parser`、`plugins`、そして`settings`プロパティのデフォルト値の設定を持っています。異なる設定を施したいのでない限り、手作業でそれらのプロパティを宣言しなおす必要はありません。何かしら他の共通設定を読み込む場合、**あなたはそれらのプロパティが上書きされたり変更されたりしないことを確認する必要があります**。さもなくば、`next`の設定と共通の動作をする設定を削除するか、上記のようにNext.jsのESLintプラグインを直接拡張することをおすすめいたします。
