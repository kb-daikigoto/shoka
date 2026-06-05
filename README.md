# Shoka（書架）— My Learning Library

技術学習記事（単一HTML）を1つの書架として集め、検索・分野で絞り込んで読める個人用学習サイト。
記事は Claude に `/create` / `/update` と頼んで増やし、`/org` で整頓して GitHub Pages で公開する。

## 構成

```
.
├── index.html        トップページ（学習ハブ）。蔵書データは末尾の ARTICLES 配列【真実】
├── catalog.json      検索用カタログ【派生】。/org が ARTICLES から再生成（手で編集しない）
├── .nojekyll         GitHub Pages で素のまま配信させる（必須・空ファイル）
├── README.md
├── skills/
│   └── learning-library/
│       └── SKILL.md  /create・/update・/org を再現する運用スキル
└── pages/            記事HTML（1記事 = 自己完結HTML）。棚(shelf)別サブディレクトリに整頓
    ├── db/        達人に学ぶ DB設計 / SQL
    ├── backend/   GraphQL / gqlgen / sqlc 系
    ├── design/    ちょうぜつソフトウェア設計入門
    ├── infra/     AWS Lambda / ALB / CloudFront / 連携
    ├── dist/      データ指向アプリケーションデザイン 第1〜4巻
    └── tools/     Git 操作 完全ガイド
```

- 記事リンクはすべて**相対パス**（`pages/<shelf>/<slug>.html`）。ユーザーサイト・プロジェクトサイトどちらでも動く。
- 記事は自己完結（内部アンカー＋外部リンクのみ、記事間リンクなし）なので、棚をまたぐ移動でも壊れない。
- トップは検索ボックス＋分野フィルタ＋タグ絞り込みを備え、記事が100件規模でも快適。

## GitHub にあげる

```bash
# このフォルダの中で
git init
git add .
git commit -m "init: learning library"
git branch -M main
# 例: 新規リポジトリ my-learning-library を作成済みとして
git remote add origin https://github.com/<ユーザー名>/<リポジトリ名>.git
git push -u origin main
```

（GitHub CLI があれば `gh repo create <名前> --public --source=. --push` でも可）

## GitHub Pages で公開する

1. リポジトリの **Settings → Pages** を開く。
2. **Build and deployment → Source** を **Deploy from a branch** にする。
3. **Branch** を `main`、フォルダを `/ (root)` にして **Save**。
4. 数十秒〜数分で公開される。
   - ユーザーサイト（リポジトリ名が `<ユーザー名>.github.io`）→ `https://<ユーザー名>.github.io/`
   - プロジェクトサイト → `https://<ユーザー名>.github.io/<リポジトリ名>/`
5. 各記事は `…/pages/<shelf>/<slug>.html`。

> `.nojekyll` を必ず含めること。GitHub Pages の既定（Jekyll）による余計な処理を無効化し、
> ファイルをそのまま配信させるため。

## 記事を増やす・直す（Claude）

新しいチャットの冒頭に `skills/learning-library/SKILL.md` の内容を貼る（またはスキルとして読み込ませる）と、
次の合言葉が使える。**実行時は最新の index.html（更新なら対象記事も）を一緒にアップロードする。**

| コマンド | 用途 |
|---|---|
| `/create [書籍名 または 学習内容]` | 新記事を `pages/<棚>/` に作り、ハブ（ARTICLES）と catalog.json に追記 |
| `/update [識別ワード or file名] [変更内容]` | catalog.json で対象記事を一意に特定し、変更範囲を確認のうえ外科的に編集 |
| `/org` | `pages/` を棚別サブディレクトリに整頓し、catalog.json を再生成して参照パスを揃える |
| `/publish [メッセージ]` | 変更を commit & push し、公開サイトに反映（git リポジトリのある環境専用） |

`/update` の第1引数は正確なファイル名でなくてよい。記事を一意に特定できる言葉でも解決する。
例: `/update sql指南書 〇〇に変更して` → `pages/db/sql-mastery.html` を特定して編集。

`/create`・`/update`・`/org` はファイルを提示するところまで（自動pushはしない）。公開したいときに
続けて **`/publish`** を叩くと commit & push まで行い、GitHub Pages に反映される。

## 新しい記事を手で追加する場合

1. 記事HTMLを `pages/<shelf>/<slug>.html`（棚に合うサブディレクトリ）に置く。
2. `index.html` の `ARTICLES:START 〜 ARTICLES:END` の間にメタ情報を1件追記（テンプレは SKILL.md 参照）。
3. Claude に `/org` を頼んで `catalog.json` を再生成（または手で1件追記）。
4. コミット → push。
