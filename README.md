# ama434.dev

ama434 の個人サイトを管理するリポジトリです。トップページ、プロフィール、ブログ、発表資料の一覧を一つのサイトにまとめ、https://ama434.dev で公開する予定です。サイト全体を 1 つのリポジトリでビルドし、1 回のデプロイで公開する方針をとります。

## 現在の状態

Astro で雛形を作りました。`/`、`/about/`、`/blog/`、`/slide/` に仮のページがあり、共通レイアウトと仮の CSS を使っています。Astro をこのまま使い続けるかは、雛形を見て判断します。React、Markdown のコンテンツ、テスト、CI、公開設定はまだありません。

- パッケージマネージャー: pnpm。バージョン条件は package.json の `devEngines.packageManager`（`^11.9.0`）で管理します。
- Node.js: `.node-version` で 26 系（2026 年 11 月時点の Active LTS）を指定しています。Astro 7 が求める最低バージョンは 22.23.4 です。
- 依存パッケージ: `astro` のみ。

## 開発

```sh
pnpm install
pnpm dev      # 開発サーバー（http://localhost:4321/）
pnpm build    # dist/ に静的ファイルを生成
pnpm preview  # 生成した dist/ をローカルで確認
```

## URL の役割

| URL | 役割 |
| --- | --- |
| `/` | 軽量なトップページ。各ページへの入口 |
| `/about` | プロフィール。旧サイトの内容を書き直して載せる |
| `/blog` | 軽いブログ。記事一覧と各記事 |
| `/slide` | 発表資料の一覧と、各資料へのリンク |

サイトはドメインのルートで公開するので、`/about` のようなサブパスをベースパスには設定しません。

## 構成

Astro による静的生成で作っています。Markdown で本文を書けることと、React の経験を活かしながら、ブラウザで動く JavaScript を操作が必要な部品だけに限れることが理由です。

方針と案は次のとおりです。導入済みのものと、まだ案の段階のものがあります。

- 雛形は Astro の minimal テンプレートを元に作った。既存のテーマは使わず、見た目と文字組みは自前の CSS で書く（導入済み。今の CSS は仮）。
- 文字組みはまず CSS で指定する。JavaScript で改行位置を計算する組版（Knuth-Plass など）は、CSS で組んだサイトを公開してから検討する。
- 本文は Markdown で書き、MDX は必要になったときだけ使う（未導入）。
- React は操作を伴う部品にだけ使う（未導入）。
- ブログ記事の frontmatter は Content Collections で検証する（未導入）。
- `/slide` は発表資料の一覧とリンクから始める。サイト内でスライドを再生する仕組みは今のところ作らない。
- DB、CMS、SSR、monorepo、大きな UI ライブラリは、必要が生じるまで入れない。

ディレクトリ構成は次のとおりです。「（未作成）」の付いたものは、今後作る予定の案です。

```text
astro.config.mjs
package.json
pnpm-lock.yaml
tsconfig.json
.node-version
wrangler.jsonc            （未作成）
.github/workflows/        （未作成）
public/                   （未作成）
src/
  pages/
    index.astro
    about.astro
    blog/index.astro
    blog/[...slug].astro  （未作成）
    slide/index.astro
  layouts/
    BaseLayout.astro
  components/             （未作成）
  content/                （未作成）
    about.md
    blog/
    slides/
  content.config.ts       （未作成）
  styles/
    global.css
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

## 進め方（案）

次の順で進める案です。まだ確定していません。

1. Astro を採用するかと、Node.js のバージョンを決める。（Astro で雛形を作って試している。Node.js は 26 系を指定した）
2. 雛形を作る。`/`、`/about/`、`/blog/`、`/slide/` に仮のページを置き、共通レイアウトと最低限の CSS を用意して、ビルドが通るようにする。（済み）
3. Cloudflare につなぎ、作業ブランチのプレビュー URL で 4 ページが見られる状態にする。
4. `/about` の本文の書き直し、デザインと文字組みの CSS、ブログの仕組み、`/slide` の一覧を少しずつ進める。

ドメインの切り替えと旧 URL の転送は、`/about` の内容ができてから行います。

日本語向けの組版エンジン（JavaScript で改行位置や字間を最適化するもの）は、このリポジトリには含めず、別のリポジトリで試作する案があります。作るかどうかは未決定で、サイトの公開はこれを待ちません。

## 未決定のこと

- Astro を使い続けるか（雛形で試している）
- React の導入時期
- Cloudflare のビルド・公開・転送設定と、切り替えの具体的な手順
- `/docs/works` と `/docs/links` の移行先
- デザインと各ページの本文
- 発表資料の掲載形式
- 日本語向けの組版エンジンを作るかどうかと、その公開方法

## ライセンス

ソフトウェア部分には [MIT License](LICENSES/MIT.txt)、文章と自作の非ソフトウェア作品には [CC BY-SA 4.0](LICENSES/CC-BY-SA-4.0.txt) を適用します。文章にはプロフィール、ブログ記事、発表資料と、この README などの文書を含みます。ソフトウェア作品とコード例は原則として MIT の対象です。

適用範囲、クレジットと継承の条件、第三者の素材の扱いは [LICENSE.md](LICENSE.md) を参照してください。二つのライセンスは対象別に適用するもので、同じ対象について自由に選べる指定ではありません。
