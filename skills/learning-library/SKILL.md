---
name: learning-library
description: >-
  個人の技術学習サイト「学習書架（My Learning Library）」を運用するスキル。ユーザーが
  「/create [書籍名・学習内容]」と送ったら新しい学習記事(単一HTML)を生成して pages/ に置き、
  トップページ index.html の ARTICLES 配列に1件追記する。「/update [file名] [変更内容]」と
  送ったら、変更範囲を先に確認したうえで該当箇所だけを外科的に編集する。技術書・技術トピックの
  学習記事を作る／既存記事を直す／学習ハブ(index)を更新する、GitHub Pages 用の自己完結HTMLを
  作る、といった依頼では、明示的に「スキルを使って」と言われなくても必ずこのスキルを使うこと。
  「学習記事を作って」「この本でまとめて」「記事を直して」「ハブに追加して」等も対象。
---

# 学習書架（Learning Library）運用スキル

個人の技術学習サイトを育てるためのスキル。記事は単一HTML（自己完結）、目録は `index.html` の
`ARTICLES` 配列。GitHub Pages にそのまま置ける相対リンク構成。

> **重要（状態の前提）**: Claude はチャットをまたいでファイルを記憶しない。`/create`・`/update`
> 実行時は、ユーザーに**最新の index.html（および更新対象の記事HTML）をアップロードしてもらい**、
> それを「現在の正」として編集する。アップロードが無ければ、まず依頼すること。

## ディレクトリ構成

```
/
├── index.html              トップページ（学習ハブ）。蔵書データ = ARTICLES 配列
├── README.md
├── .nojekyll               GitHub Pages で素のまま配信させる空ファイル
├── skills/learning-library/SKILL.md   このスキル
└── pages/                  記事HTML（1記事 = 自己完結HTML）
    └── <slug>.html
```

リンクはすべて相対パス（`pages/<slug>.html`）。ルート公開でもプロジェクト公開でも動く。

---

## コマンド: `/create [書籍名 または 学習内容]`

1. **レベル確認（必要なときだけ）**: トピックの難度・対象が曖昧なら、着手前に選択式で2つ確認する。
   自明な場合は聞かずに進めてよい。
   - 現在のレベル: ①ほぼ初めて ②基礎はある ③実務でそこそこ ④熟練（教えられる）
   - 目標レベル: ①全体像をつかむ ②実務で使える ③仕組みまで深く ④専門家レベルで応用
2. **裏取り**: URLがあれば web_fetch、現在の事実（価格・最新仕様・役職等）は web_search で確認。
3. **frontend-design スキルを読む**（`/mnt/skills/public/frontend-design/SKILL.md`）。
4. **記事を生成**: 下記「記事デザイン規約」に従い、`pages/<slug>.html` に自己完結HTMLを作成。
5. **ハブに追記**: アップロードされた `index.html` の `ARTICLES:START 〜 ARTICLES:END` の間に、
   新しいオブジェクトを**1件だけ**追記（既存要素・他の箇所は変更しない）。
   新分野なら `SHELVES` にも棚を1つ追加。`st-upd`（最終更新月）は更新してよい。
6. **両方を提示**: `pages/<slug>.html` と更新済み `index.html` を present_files で出す。

### slug 命名
英小文字＋ハイフン。例: `refactoring.html`, `tcp-ip.html`。シリーズ物は `name-vol1.html`。

### ARTICLES 1件テンプレ（index.html に貼る形）
```js
{file:"pages/<slug>.html", shelf:"<SHELVESのid>", accent:"#RRGGBB", cat:"CATEGORY",
 title:"表示タイトル", book:"書名/出典", levelFrom:"初級", levelTo:"中級",
 desc:"1〜2文の要約。",
 tags:["タグ1","タグ2","タグ3"],
 vol:"全N巻 第X巻"}   // ← シリーズでなければ vol は省略
```
既存 shelf id: `db`(データの土台) / `backend`(バックエンド&API) / `design`(ソフトウェア設計) /
`infra`(クラウド&インフラ) / `dist`(分散データシステム) / `tools`(開発ツール)。
新分野が必要なら SHELVES に `{id, label, sub}` を追加する。

---

## コマンド: `/update [file名] [変更内容]`

意図しない箇所を変えないことが最優先。

1. 対象ファイルを受け取る（アップロード必須。無ければ依頼）。
2. **変更計画を先に提示**: 「変更する点」「変更しない点」を箇条書きで明文化する。
3. 指示が曖昧・複数解釈できるなら、**着手せず**に質問を重ねて確定させる。
4. 確定後、`str_replace` で**該当箇所だけ**を編集。全体の再生成はしない
   （明示的に「作り直して」と言われた場合を除く）。
5. 更新後ファイルを present_files で出し、変更点を一覧で報告。

---

## 記事デザイン規約（シリーズで体裁を揃える）

- **1記事 = 単一の自己完結HTML**。CSS/JS は全てインライン。外部依存は Google Fonts のみ。
  ブラウザストレージ不使用。日本語。対象は初級→中級を基本に「他資料が要らない網羅度」。
- 共通の骨格:
  - 左に sticky な番号付き目次（IntersectionObserver で現在地ハイライト）
  - 上部にスクロール進捗バー、右下に「トップへ戻る」
  - ヒーロー（バッジ＋kicker＋大見出し＋lede＋メタchip）
  - セクションは 01,02… の等幅番号。長い記事は Part で束ねる
  - 各章冒頭に「一言でいうと」ボックス。理屈と具体例・コードを交互に
  - コールアウト 5種: 理屈(why)/要点(point)/補足(tip)/注意(warn)/危険(danger)
  - コードはダーク背景・トークン色分け・コピーボタン。CLIは `$` プロンプト
  - 図は inline SVG（角丸ボックス＋矢印マーカー＋アクセント色）。捏造数値・存在しない概念は不可
  - 比較は table、概念整理は cards、末尾にチートシート＋用語集
- **トピックごとに個性を変える**: アクセント色・フォントを変え「シリーズ感はあるが各々識別できる」状態に。
  汎用AI調フォント（Inter/Roboto/Arial 等）は避ける。日本語対応フォント＋等幅をGoogle Fontsから。
  既使用の目安（重複回避）: DB設計=teal+Shippori Mincho / SQL=indigo+Zen Old Mincho /
  ソフト設計=rose+Zen Maru Gothic / DDIA=ダーク+cyan&amber+Noto Serif JP / ハブ=parchment+Kaisei Tokumin。
- 著作権配慮: 原文の長い引用・逐語コピーをしない。自分の言葉で噛み砕く。
  末尾に「簡略化／仕様は変わりうる、最新は公式ドキュメントで」の注記。
- 温かく簡潔な口調。過剰な前置き・後書きはしない。

---

## GitHub Pages デプロイ（要約）

1. リポジトリに `index.html` / `pages/` / `.nojekyll` / `README.md` / `skills/` を配置。
2. push。
3. Settings → Pages → Source: *Deploy from a branch*、Branch `main` / `/(root)`。
4. 公開: ユーザーサイト `https://<user>.github.io/`、記事 `…/pages/<slug>.html`。

詳しい手順は README.md を参照。

## クイックリファレンス

| コマンド | 用途 | 必要な添付 |
|---|---|---|
| `/create [トピック]` | 新記事を作りハブに追加 | 最新 index.html |
| `/update [file] [内容]` | 既存ファイルを手直し | 対象ファイル |

合言葉: **「真実は1つ、ビューは派生」**。記事は `pages/`、目録は `index.html` の `ARTICLES`。
