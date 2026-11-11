# 進捗記録

作業の経過と、次の作業者（人またはエージェント）への引き継ぎ事項を書きます。サイトの現状と方針の正本は [README.md](../README.md) と [AGENTS.md](../AGENTS.md) です。

## 2026-11-11 雛形の作成（ブランチ `docs/next-steps`）

クラウド環境（Claude Code on the web）で作業し、ローカルのセッションに引き継ぎます。main にはマージしておらず、PR もまだありません。

### やったこと

- README に「進め方（案）」を追加し、AGENTS.md にその順番で進めることを追記した（`3ec6e29`）。
- Astro の minimal テンプレートを元に雛形を作った（`2f61639`）。
  - `/`、`/about/`、`/blog/`、`/slide/` に「準備中」とだけ書いた仮ページ。個人情報は入れていない。
  - 共通レイアウト `src/layouts/BaseLayout.astro`: ナビゲーション（現在のページに `aria-current`）、フッターに作者名、CC BY-SA 4.0 へのリンク、MIT の表示。
  - 仮の CSS `src/styles/global.css`: 本文幅 40rem、行間 1.8、`line-break: strict`、`text-wrap: pretty`。あとで自前のデザインに書き直す前提。
  - ファビコンは入れていない（テンプレートのものは Astro のロゴのため）。
  - README と AGENTS.md の現状説明を更新し、README に開発コマンドを追加した。

### 作業中に決めたこと

- Node.js は `.node-version` で 26 系を指定した（2026-11 時点の Active LTS。nodejs.org の一覧で確認）。
- package.json の `devEngines.runtime` で Node.js を自動ダウンロードさせる設定は入れていない。クラウド環境ではダウンロード先がブロックされて動かなかったため。ローカルでは nvm、fnm、mise などで `.node-version` に合わせる。
- Astro は 7.4.8。作業時点の最新は 7.4.11 だったが、公開直後で pnpm 11 の公開日数の制限に引っかかり、入れると `pnpm-workspace.yaml` に除外設定が自動で追加される。安全のための仕組みを外さないよう、制限を満たす版を入れた。

### 実施した検証

- `pnpm build` が通り、4 ページが生成されること（Node.js 26.13.1、pnpm 11.28.11）。
- 生成された HTML の内部リンク（`/`、`/about/`、`/blog/`、`/slide/`）が、すべて生成済みのページを指していること。
- 開発サーバーで 4 ページとも HTTP 200 が返ること。
- Chromium で `/about/` をスマートフォン幅（390px）とデスクトップ幅で撮影して、表示を確認したこと。フッターのリンク前の空白が消える問題を見つけて直した。

### 未実施の検証

- 型チェック（`astro check`）。`@astrojs/check` と `typescript` を入れていない。
- `pnpm preview` での確認。
- Chromium 以外のブラウザでの表示。
- Cloudflare へのデプロイ（設定もまだない）。

### 次にやること

1. ローカルで `pnpm install` と `pnpm dev` を実行し、雛形の見た目を確認する。Astro を使い続けるか判断する。
2. Astro を使い続けるなら、PR を作って main にマージする。ブランチ名は文書用の `docs/next-steps` のままなので、必要なら名前を変える。
3. README の「進め方（案）」の 3 番目にあたる、Cloudflare（`wrangler.jsonc` と Workers Builds）の設定に進む。Workers Builds との連携は Cloudflare の管理画面での作業になる。
