# ama434.dev

ama434 の個人サイトを管理するリポジトリです。トップページ、プロフィール、ブログ、発表資料の一覧を一つのサイトにまとめ、https://ama434.dev で公開する予定です。サイト全体を 1 つのリポジトリでビルドし、1 回のデプロイで公開する方針をとります。

## 現在の状態

`pnpm init` で package.json を作成済みです。パッケージマネージャーには pnpm を使い、バージョン条件は package.json の `devEngines.packageManager` で管理します。依存パッケージ、アプリケーション、開発・ビルド・テスト用のスクリプト、CI、公開設定はまだありません。

## URL の役割

| URL | 役割 |
| --- | --- |
| `/` | 軽量なトップページ。各ページへの入口 |
| `/about` | プロフィール。旧サイトの内容を書き直して載せる |
| `/blog` | 軽いブログ。記事一覧と各記事 |
| `/slide` | 発表資料の一覧と、各資料へのリンク |

サイトはドメインのルートで公開するので、`/about` のようなサブパスをベースパスには設定しません。

## 構成案（未導入）

Astro、React、TypeScript による静的生成を候補として検討しています。どれもまだ導入しておらず、Astro の採用も確定していません。この案を選んだ理由は二つあります。Markdown で本文を書けることと、React の経験を活かしながら、ブラウザで動く JavaScript を操作が必要な部品だけに限れることです。

案の中身は次のとおりです。

- 本文は Markdown で書き、MDX は必要になったときだけ使う。
- 共通のページとレイアウトは Astro で作り、React は操作を伴う部品にだけ使う。
- ブログ記事の frontmatter は Content Collections で検証する。
- `/slide` は発表資料の一覧とリンクから始める。サイト内でスライドを再生する仕組みは今のところ作らない。
- DB、CMS、SSR、monorepo、大きな UI ライブラリは、必要が生じるまで入れない。

この案どおりに作った場合のディレクトリ構成は、たとえば次のようになります。package.json 以外は現在存在しません。

```text
（構成案）
astro.config.mjs
package.json
tsconfig.json
wrangler.jsonc
.github/workflows/
public/
src/
  pages/
    index.astro
    about.astro
    blog/index.astro
    blog/[...slug].astro
    slide/index.astro
  layouts/
  components/
  content/
    about.md
    blog/
    slides/
  content.config.ts
  styles/
```

## 公開基盤

公開基盤には Cloudflare Workers Static Assets を採用します。静的に生成した HTML、CSS、JavaScript、画像を配信し、当初は Worker の処理コードや SSR を導入しない方針です。設定とデプロイはまだ行っていません。

GitHub の `ama434/ama434.dev` を Workers Builds に接続し、`main` を本番公開、作業ブランチをプレビューに使う予定です。独自ドメインは `ama434.dev` とし、公開設定は Wrangler の設定ファイルで管理します。

旧サブドメインからの転送には Cloudflare の Redirect Rules を使う方針です。サイト内のパス単位の転送と、ホスト名を条件とする旧ドメインからの転送を分けて管理します。具体的な URL 対応と切り替え手順は、内容の移行先を決めてから設定します。

仕様は [Workers Static Assets](https://developers.cloudflare.com/workers/static-assets/) と [Workers Builds](https://developers.cloudflare.com/workers/ci-cd/builds/) の公式ドキュメントを参照してください。

## 旧サイトからの移行

移行元は https://about.ama434.dev で公開している旧サイトで、ソースは https://github.com/ama434/about-ama434 にあります。旧サイトは VitePress で作っており、トップ、プロフィール、制作物、リンクの各ページを持っています。旧リポジトリは参照するだけで、編集はしません。

旧サイトのプロフィールなどの個人情報は、現在も正しいとは限りません。そのまま自動で書き換えて移さず、内容を確認しながら書き直します。

旧 URL は、しばらくのあいだ新サイトの対応するページへ転送する方針です。対応先のない旧 URL まで一律にトップへ送ると、読み手が探していた情報にたどり着けないため、ページごとに転送先を決めます。

| 旧 URL | 新サイトの転送先（移行案） |
| --- | --- |
| `https://about.ama434.dev/` | `https://ama434.dev/about/` |
| `https://about.ama434.dev/docs/about` | `https://ama434.dev/about/`（プロフィールを統合する案） |
| `https://about.ama434.dev/docs/works` | `/about` 内、または必要に応じて作る下位ページ（未決定） |
| `https://about.ama434.dev/docs/links` | `/about` 内、または必要に応じて作る下位ページ（未決定） |

旧サブドメインからの転送には HTTP の 301 または 308 を使い、転送を続ける期間は旧サブドメインの DNS と HTTPS も維持します。

## 未決定のこと

- Astro を含む構成の採用
- Cloudflare のビルド・公開・転送設定と、切り替えの具体的な手順
- `/docs/works` と `/docs/links` の移行先
- デザインと各ページの本文
- 発表資料の掲載形式
- Node.js のバージョン

## ライセンス

ソフトウェア部分には [MIT License](LICENSES/MIT.txt)、文章と自作の非ソフトウェア作品には [CC BY-SA 4.0](LICENSES/CC-BY-SA-4.0.txt) を適用します。文章にはプロフィール、ブログ記事、発表資料と、この README などの文書を含みます。ソフトウェア作品とコード例は原則として MIT の対象です。

適用範囲、クレジットと継承の条件、第三者の素材の扱いは [LICENSE.md](LICENSE.md) を参照してください。二つのライセンスは対象別に適用するもので、同じ対象について自由に選べる指定ではありません。
