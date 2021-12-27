---
description: Next.jsはpublicディレクトリ内の画像など、静的ファイルを配信することもできます。ここではこの機能がどのようにはたらくかを学びましょう。
---

# 静的ファイルの配信

Next.jsはプロジェクトのルートの`public`という名前のフォルダーから、画像などの静的ファイルを配信することができます。`public`内のファイルは、アプリのコード内からベースURL（`/`）で始まるパスで参照することができます。

例えば、`public/me.png`に画像を追加すると、次のコードが画像にアクセスできるようになります：

```jsx
import Image from 'next/image'

function Avatar() {
  return <Image src="/me.png" alt="me" width="64" height="64" />
}

export default Avatar
```

> 注意： `next/image`を利用するにはNext.jsバージョン10以降である必要があります。

このフォルダーはまた`robots.txt`、`favicon.ico`、Googleによるサイト所有権確認のためのファイル、その他のあらゆる静的ファイル（`.html`ファイルも含む）のために利用できます！

> **注意**：`public`ディレクトリの名前は変更できません。そしてこのディレクトリだけが静的アセットの配信に利用されます。

> **注意**：`pages/`ディレクトリと同じ名前のファイルを作らないようにしてください。エラーの原因になります。
>
> こちらも参照してください： [https://nextjs.org/docs/messages/conflicting-public-file-page](https://nextjs.org/docs/messages/conflicting-public-file-page)

> **注意**：[ビルド時](/docs/api-reference/cli.md#build)に `public`内に存在したアセットだけがNext.jsにより配信されます。ランタイムで追加されたファイルは利用できません。ファイル・ストレージのため[AWS S3](https://aws.amazon.com/s3/)のようなサード・パーティ・サービスを利用することをおすすめいたします。
