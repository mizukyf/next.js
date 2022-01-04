---
description: あなたのNext.jsアプリケーションで環境変数を追加しそれにアクセスする方法を学びましょう。
---

# 環境変数

> このドキュメントはNext.jsバージョン9.4以上向けのものです。もしこれより古いバージョンを使用されている場合は、アップグレードを行うか、[next.config.jsにおける環境変数](/docs/api-reference/next.config.js/environment-variables.md)を参照してください。

<details open>
  <summary><b>例</b></summary>
  <ul>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/environment-variables">環境変数の例</a></li>
  </ul></details>

Next.jsは環境変数を組み込みサポートしています。これにより次のことが可能です：

- [`.env.local`を使い環境変数を読み込む](#loading-environment-variables)
- [`NEXT_PUBLIC_`というプレフィクスを付与することで環境変数をブラウザ側に提示する](#exposing-environment-variables-to-the-browser)

## 環境変数を読み込む

Next.jsは環境変数を`.env.local`から`process.env`へと読み込む機能を組み込みでサポートしています。

`.env.local`の例：

```bash
DB_HOST=localhost
DB_USER=myuser
DB_PASS=mypassword
```

これにより`process.env.DB_HOST`、`process.env.DB_USER`、そして `process.env.DB_PASS` がNode.js環境に自動的に読み込まれ、[Next.jsのデータ取得メソッド群](/docs/basic-features/data-fetching.md)と[API routes](/docs/api-routes/introduction.md)のコードから利用可能になります。

例えば、[`getStaticProps`](/docs/basic-features/data-fetching.md#getstaticprops-static-generation)を使う場合：

```js
// pages/index.js
export async function getStaticProps() {
  const db = await myDB.connect({
    host: process.env.DB_HOST,
    username: process.env.DB_USER,
    password: process.env.DB_PASS,
  })
  // ...
}
```

> **注意**：機密情報をサーバーサイドに留めておくため、Next.jsはビルド時に`process.env.*`の値を正しい値に置き換えます。このため`process.env`は標準的なJavaScriptオブジェクトではなく、<br>[オブジェクトの分割代入](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)ができません。例えば、`const { PUBLISHABLE_KEY } = process.env`*ではなく*、`process.env.PUBLISHABLE_KEY`である必要があります。

> **注意**：Next.jsは`.env*`ファイルの中の変数（`$VAR`）を自動的に展開します。この機能を使うとある値の定義箇所で別の値を参照することができます：
>
> ```bash
> # .env
> HOSTNAME=localhost
> PORT=8080
> HOST=http://$HOSTNAME:$PORT
> ```
>
> 実際の値として`$`を使用したい場合は、次のようにしてエスケープする必要があります：`\$`.
>
> 例えば：
>
> ```bash
> # .env
> A=abc
>
> # "preabc" となる
> WRONG=pre$A
>
> # "pre$A" となる
> CORRECT=pre\$A
> ```

## 環境変数をブラウザに提示する

デフォルトでは環境変数はNode.js環境においてのみ利用可能です。これはつまりそれらの変数はブラウザに対して提示されないということです。

環境変数をブラウザに対して提示するには、変数に`NEXT_PUBLIC_`というプレフィクスを付与する必要があります。例えば：

```bash
NEXT_PUBLIC_ANALYTICS_ID=abcdefghijk
```

これによりNode.js環境に自動的に`process.env.NEXT_PUBLIC_ANALYTICS_ID`が読み込まれ、あなたのコードのどこからでもアクセスが可能になります。`NEXT_PUBLIC_`というプレフィクスが付いているので、この値はJavaScriptコード内にインライン化されてブラウザに送信されます。このインライン化はビルド時に行われます。このため各々の`NEXT_PUBLIC_`環境変数はプロジェクトのビルド時に設定されている必要があります。

```js
// pages/index.js
import setupAnalyticsService from '../lib/my-analytics-service'

// NEXT_PUBLIC_というプレフィクスが付いているので、NEXT_PUBLIC_ANALYTICS_ID はここで利用できる
setupAnalyticsService(process.env.NEXT_PUBLIC_ANALYTICS_ID)

function HomePage() {
  return <h1>こんにちは、皆さん</h1>
}

export default HomePage
```

## 環境変数のデフォルト値

一般的にはただ1つ`.env.local`ファイルだけが必要となります。しかしながら、時折`development`（開発環境）（`next dev`）や`production`（本番環境）（`next start`）ごとにデフォルトの値を追加したいこともあるでしょう。

Next.jsでは、`.env`（すべての環境で有効）、`.env.development`（開発環境で有効）、 そして`.env.production`（本番環境で有効）で、デフォルト値を定義することができます。

`.env.local`は常にデフォルト値を上書きします。

> **注意**：`.env`、`.env.development`、そして `.env.production`ファイルは、デフォルト値の定義のためにあなたのリポジトリに含めておくべきです。一方、バージョン管理対象であることを示すために**`.env.*.local`は`.gitignore`に書き加えておくべき**です。`.env.local`は機密情報を保管することができる場所です。

## Vercelプラットフォームの環境変数

Next.jsアプリケーションを[Vercel](https://vercel.com)にデプロイする場合[プロジェクトのセッティング画面で](https://vercel.com/docs/environment-variables)環境変数を設定できます。

すべてのタイプの環境変数はここで設定すべきです。これには開発環境において使用する環境変数も含まれます。それらはその後[あなたのローカル・デバイスにダウンロード](https://vercel.com/docs/environment-variables#development-environment-variables)されます。

[開発環境の環境変数](https://vercel.com/docs/environment-variables#development-environment-variables)を設定している時は、次のコマンドを実行することで、あなたのローカル・マシーンで使用するために`.env.local`をプルすることができます：

```bash
vercel env pull .env.local
```

Vercel CLIをデプロイに使用する場合、プロジェクトに必ず[`.vercelignore`](https://vercel.com/guides/prevent-uploading-sourcepaths-with-vercelignore?query=vercelignore#allowlist)ファイルを追加しましょう。このファイルはアップロードすべきでないファイルの一覧を含んでおり、それらは一般的には`.gitignore`ファイルに含まれるのと同じになります。

## テスト環境の環境変数

`development`（開発環境）と`production`（本番環境）とは別に、第3の選択肢があります。それは `test`（テスト環境）です。開発環境や本番環境のためのそれと同じように、`.env.test`ファイルを使うことで、テスト環境のためのデフォルト値を定義することができます（それらの値は前述の2環境とは共有されません）。

この方法は`jest`や`cypress`のようなツールを利用してテストを実行する際に便利です。このようなケースではテストだけを目的とした環境変数を設定する必要があるからです。テスト環境用のデフォルト値は`NODE_ENV`に`test`が設定されている時にロードされますが、この値はふつうテスト・ツールが設定してくれるため、あなた自身の手で何かをする必要はありません。

`test`環境と、`development`および`production`の両環境との間には、心に留めておくべき小さな違いがあります：`.env.local`は読み込まれません。<br>これは、同じテストはいずれの開発メンバーの環境でも同じ結果となることが期待されるためです。開発メンバー各自の環境の`.env.local`（これはデフォルト値をオーバーライドするために利用されます）を無視して実行することで、 各テストで同じ環境変数のデフォルト値を利用することになります。

> **注意**：環境変数のデフォルト値と同様に、`.env.test`はリポジトリに含めるべきですが、`.env.test.local`は含めるべきではありません。 `.env*.local`は`.gitignore`によりバージョン管理外にすべきです。

単体テスト実行中は、`@next/env` パッケージが提供する`loadEnvConfig`関数を利用して、Next.jsが行うのと同じ方法で環境変数を読み込むようにすることができます。

```js
// 下記のコードはJestのグローバル・セットアップ・ファイルやその類似の
// テスト実行のセットアップ・コードで利用することができます
import { loadEnvConfig } from '@next/env'

export default async () => {
  const projectDir = process.cwd()
  loadEnvConfig(projectDir)
}
```
