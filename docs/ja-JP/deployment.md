---
description: あなたのNext.jsアプリをVercelとその他のホスティングの選択肢を用いてプロダクション・リリースしましょう。
---

# デプロイメント

## Vercel（推奨）

Next.jsをプロダクション・リリースするもっとも簡単な方法はNext.jsの作成者たちが提供する**[Vercelプラットフォーム](https://vercel.com)**を利用するものです。[Vercel](https://vercel.com)は静的サイト、ハイブリッド・アプリ、そしてサーバーレス関数のためのクラウド・プラットフォームです。

### さあ、はじめましょう

もしまだお済みでなければ、あなたのNext.jsアプリをあなたが選んだプロバイダーが提供するGitリポジトリにプッシュしましょう：[GitHub](https://github.com/)、[GitLab](https://gitlab.com/)、もしくは[BitBucket](https://bitbucket.org/)。あなたのリポジトリはプライベートなものでも公開されたものでも構いません。

それが済んだら、次の手順に従って進めてください：

1. [Vercelにサインアップします](https://vercel.com/signup)（クレジットカードは必須ではありません）。
2. サインアップを終えると、[“Import Project”](https://vercel.com/new)ページが表示されます。“From Git Repository” の下の選択肢から、あなたが使用しており、これからVercelプラットフォームに統合するGitリポジトリ・プロバイダーを選択してください（選択肢：[GitHub](https://vercel.com/docs/git/vercel-for-github)、[GitLab](https://vercel.com/docs/git/vercel-for-gitlab)、[BitBucket](https://vercel.com/docs/git/vercel-for-bitbucket)）。
3. セットアップを終えたら、 “Import Project From …”  をクリックして、あなたのNext.jsアプリをインポートします。あなたのアプリがNext.jsを利用していることが自動検知され、ビルド設定が作られます。何も変更する必要はありません。すべてうまく行くはずです！
4. インポート後、あなたのNext.jsアプリはデプロイされ、デプロイ先のURLが示されます。“Visit”をクリックすればプロダクション・リリースされたあなたのアプリがご覧になれます。

おめでとうございます！　あなたのNext.jsアプリがデプロイされました！　もし不明点がおありの場合は[Vercelのドキュメント](https://vercel.com/docs)をご覧になってください。

> [独自のサーバー](/docs/advanced-features/custom-server.md)をご利用になっている場合は、そこから移行することを強くおすすめします（例えば、[動的ルーティング](/docs/routing/dynamic-routes.md)を用いて）。移行ができない場合は、[その他のホスティングの選択肢](#other-hosting-options)をお考えください。

### DPS：開発、プレビュー、発送

私たちがおすすめするワークフローについてご説明しましょう。[Vercel](https://vercel.com)は、私たちが**DPS**と呼ぶワークフローをサポートしています：**D**evelop（開発）、**P**review（プレビュー）、そして**S**hip（発送）：

- **Develop（開発）：**Next.jsでコードを書きましょう。開発サーバーを起動しっぱなしにして、[React Fast Refresh](https://nextjs.org/blog/next-9-4#fast-refresh)の恩恵を享受しましょう。
- **Preview（プレビュー）：**あなたがGitHub、GitLab、ないしBitBucketのブランチに対して変更をプッシュすると、Vercelは自動で独自のURLにデプロイを行います。このURLはGitHubのプル・リクエストやVercelのプロジェクト・ページの “Preview Deployments” でご覧になれます。[より詳しく学ぶにはこちらをご覧ください](https://vercel.com/features/deployment-previews)。
- **Ship（発送）：** 発送の順が整ったら、あなたのデフォルト・ブランチ（例えば`main`）にプル・リクエストをマージしてください。Vercelが自動的にプロダクション・リリースを行います。

DPSワークフローを利用することで、*コード・レビュー*のついでに*デプロイ結果のプレビュー*を行うことができます。デプロイの都度独自のURLが生成されるので、あなたはそれを関係者に共有したり統合テストのために利用したりすることができます。

### Next.jsのための最適化

[Vercel](https://vercel.com)はNext.jsの製作者たちにより作られたので、Next.jsに対するサポートも最高級のものです。

例えば、[ハイブリッド・ページ](/docs/basic-features/pages.md)のアプローチが最初から完全にサポートされています。

- すべてのページについて[静的生成](/docs/basic-features/pages.md#static-generation)や[サーバーサイド・レンダリング](/docs/basic-features/pages.md#server-side-rendering)が利用可能です。
- [静的生成](/docs/basic-features/pages.md#static-generation)を利用するページとアセット（JS、 CSS、画像、フォント、その他）は、自動的にV[Vercelのエッジ・ネットワーク](https://vercel.com/docs/edge-network/overview)から配信され、驚くほど高速です。
- [サーバーサイド・レンダリング](/docs/basic-features/pages.md#server-side-rendering)を利用するページと[APIルート](/docs/api-routes/introduction.md)は自動的に隔離されたサーバーレス関数になります。これによりページ・レンダリングとAPIリクエスト処理に無限大のスケーラビリティが付与されます。

### 独自ドメイン、環境変数、自動的なHTTPS、その他

- **独自ドメイン：**ひとたび[Vercel](https://vercel.com)にデプロイが終わったら。あなたのNext.jsアプリに独自ドメインを付与することができます。[こちらのドキュメント](https://vercel.com/docs/custom-domains)をご覧ください。
- **環境変数：**Vercel上で環境変数を設定することもできます。[こちらのドキュメント](https://vercel.com/docs/environment-variables)をご覧ください。設定が終わるとあなたのNext.jsアプリで[これらの環境変数を利用](/docs/api-reference/next.config.js/environment-variables.md)できます。
- **自動的なHTTPS：**HTTPSはデフォルトで有効になります（独自ドメインも含めて）。追加の設定は不要です。SSL証明書も自動で最新化されます。
- **その他：** Vercelプラットフォームについてより詳しく学ぶには[私たちのドキュメントをご覧ください](https://vercel.com/docs)。

## 自動的な更新

Next.jsアプリをデプロイした時、あなたはブラウザの再読み込みなしに最新バージョンをご覧になりたいでしょう。

Next.jsはルーティング中に背後で自動的にに最新バージョンのアプリを読み込みます。クライアントサイドのページ遷移のため、`next/link`は一時的に通常の`<a>`タグのように機能します。

**注意：`next/link`により** 遷移先のページ（古いバージョンの）がすでに事前フェッチされていた場合、Next.jsはその古いバージョンを利用します。完全なページの再読み込みもしくは複数回のクライアントサイド・ページ遷移が行わると、Next.jsは最新バージョンを表示します。

## その他のホスティングの選択肢

### Node.jsサーバー

Next.jsはNode.jsをサポートするあらゆるホスティング・プロバイダー上にデプロイ可能です。あなたのアプリの`package.json`が`"build"`と`"start"`スクリプトを含んでいることを確認してください。

```json
{
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start"
  }
}
```

`next build`で`.next`フォルダー内にプロダクション・リリース用のアプリケーションをビルドします。ビルドが終わったら、`next start`でNode.jsサーバーを起動します。このサーバーは[ハイブリッド・ページ](/docs/basic-features/pages.md)をサポートし、静的生成されたページとサーバーサイド・レンダリングされたページのいずれも提供します。

[`next/image`](/docs/basic-features/image-optimization.md)を利用している場合、よりパフォーマンスに優れた[画像最適化](/docs/basic-features/image-optimization.md)のため、プロダクション・リリース環境に`sharp`を追加することをご検討ください。プロジェクト・ディレクトリ内で`npm install sharp`を実行することで追加できます。Linux環境では、メモリの過度の消費を防ぐために`sharp`のために[追加の設定](https://sharp.pixelplumbing.com/install#linux-memory-allocator)が必要になるかもしれません。

### Dockerイメージ

<details open>
  <summary><b>例</b></summary>
  <ul>
    <li><a href="https://github.com/vercel/next.js/tree/canary/examples/with-docker">with-docker</a></li>
  </ul></details>

Next.jsは[Docker](https://www.docker.com/)コンテナをサポートするあらゆるホスティング・プロバイダーに対してデプロイ可能です。[Kubernetes](https://kubernetes.io/)や[HashiCorp Nomad](https://www.nomadproject.io/)のようなコンテナ・オーケストレーション・ツール上にアプリをデプロイをしている場合や、何らかのクラウド・プロバイダー上で稼働するシングル・ノード内でアプリを実行している場合に、このアプローチが可能です。

こちらに示すのは`node:alpine`を利用するマルチステージの`Dockerfile`です：

```Dockerfile
# 必要な場合のみ依存性をインストールする
FROM node:alpine AS deps
# libc6-compatが必要な理由についてはこちらをご確認ください： https://github.com/nodejs/docker-node/tree/b4117f9333da4138b03a546ec926ef50a31506c3#nodealpine
RUN apk add --no-cache libc6-compat
WORKDIR /app
COPY package.json yarn.lock ./
RUN yarn install --frozen-lockfile

# 必要な場合のみソースコードをリビルドする
FROM node:alpine AS builder
WORKDIR /app
COPY . .
COPY --from=deps /app/node_modules ./node_modules
RUN yarn build && yarn install --production --ignore-scripts --prefer-offline

# プロダクション・リリース用イメージ、すべてのファイルをコピーし run next を実行
FROM node:alpine AS runner
WORKDIR /app

ENV NODE_ENV production

RUN addgroup -g 1001 -S nodejs
RUN adduser -S nextjs -u 1001

# デフォルトの設定を使わない場合のみ next.config.js のコピーも必要
# COPY --from=builder /app/next.config.js ./
COPY --from=builder /app/public ./public
COPY --from=builder --chown=nextjs:nodejs /app/.next ./.next
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/package.json ./package.json

USER nextjs

EXPOSE 3000

ENV PORT 3000

# Next.jsは全般的な利用状況について完全に匿名の計測データを収集する
# 詳しく学ぶにはこちらをご確認ください： https://nextjs.org/telemetry
# 次の行をコメント解除するとこの計測を無効化できる
# ENV NEXT_TELEMETRY_DISABLED 1

CMD ["node_modules/.bin/next", "start"]
```

あなたのプロジェクト・フォルダーのルートにこのDockerfileが置かれていることを確認してください。

`docker build . -t my-next-js-app`によりコンテナをビルドできます。`docker run -p 3000:3000 my-next-js-app`によりこのコンテナを実行できます。

### 静的なHTMLのエクスポート

あなたのNext.jsアプリについて静的なHTMLのエクスポートを行いたいとお考え場合は、[私たちのドキュメント](/docs/advanced-features/static-html-export.md)の手順に沿ってやってみてください。
