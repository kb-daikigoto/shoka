# 学習書架 — My Learning Library

技術学習記事（単一HTML）を1つの書架として集め、検索・分野で絞り込んで読める個人用学習サイト。
記事は Claude に `/create` / `/update` と頼んで増やし、GitHub Pages で公開する。

## 構成

```
.
├── index.html        トップページ（学習ハブ）。蔵書データは末尾の ARTICLES 配列
├── .nojekyll         GitHub Pages で素のまま配信させる（必須・空ファイル）
├── README.md
├── skills/
│   └── learning-library/
│       └── SKILL.md  /create・/update を再現する運用スキル
└── pages/            記事HTML（1記事 = 自己完結HTML、相対リンク）
    ├── db-design.html
    ├── sql-mastery.html
    ├── software-design.html
    ├── ddia-vol1〜4.html
    ├── graphql-complete-guide.html
    ├── gqlgen-tutorial.html / gqlgen-sqlc-full.html / sqlc-intro.html
    ├── aws-lambda.html / aws-alb.html / aws-cloudfront.html / aws-integration.html
    └── git-complete-guide.html
```

- 記事リンクはすべて**相対パス**。ユーザーサイト・プロジェクトサイトどちらでも動く。
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
5. 各記事は `…/pages/<slug>.html`。

> `.nojekyll` を必ず含めること。GitHub Pages の既定（Jekyll）による余計な処理を無効化し、
> ファイルをそのまま配信させるため。

## 記事を増やす・直す（Claude）

新しいチャットの冒頭に `skills/learning-library/SKILL.md` の内容を貼る（またはスキルとして読み込ませる）と、
次の合言葉が使える。**実行時は最新の index.html（更新なら対象記事も）を一緒にアップロードする。**

| コマンド | 用途 |
|---|---|
| `/create [書籍名 または 学習内容]` | 新記事を作り、ハブ（ARTICLES）に追記 |
| `/update [file名] [変更内容]` | 既存ファイルを、変更範囲を確認のうえ外科的に編集 |

更新で受け取った新ファイルをコミット → push すれば公開サイトに反映される。

## 新しい記事を手で追加する場合

1. 記事HTMLを `pages/<slug>.html` に置く。
2. `index.html` の `ARTICLES:START 〜 ARTICLES:END` の間にメタ情報を1件追記（テンプレは SKILL.md 参照）。
3. コミット → push。
