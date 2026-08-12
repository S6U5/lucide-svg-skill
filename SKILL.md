---
name: lucide-svg
description: >
  Generate and edit Lucide-style 24x24 line icon SVGs with Lucide-format metadata JSON.
  Use when the user asks for a Lucide-style icon, custom icon, or simple line icon SVG.
  Lucideスタイルの24×24線画アイコンSVGを生成・編集するスキル。
  ユーザーがLucide形式・Lucideスタイルのアイコン、またはシンプルな線画アイコンのSVGを求めたときに使用する。
  「アイコンを作って」「Lucideっぽいアイコン」「カスタムアイコン」「〇〇アイコンのSVGコード」「JSONメタデータも欲しい」といった表現でトリガーする。
  Lucide形式アイコンのデザイン・最適化の質問にも活用してよい。
  使わない場合: イラスト・図解・装飾・ロゴ・バナー・OGP画像など、アイコン以外のSVG作成にはこのスキルを使わない
  （24×24グリッドやstroke固定などLucide専用の制約が有害になるため）。
---

# Lucide SVG アイコン生成スキル

Lucideアイコンの設計ルールに従ってSVGアイコンを生成する。
シンプルで一貫性のある線画アイコンを作ることが目的で、すべてのアイコンは同じ視覚的言語で描かれる。

**アイコンを生成するときは、SVGファイルとJSONメタデータファイルを必ずセットで出力する。**
ユーザーが「SVGだけでいい」と明示した場合のみJSONを省略する。

---

## 生成前の確認: 公式アイコンとの重複チェック

アイコンを作る前に、**依頼された概念が公式 Lucide に既に存在しないか**を考える。

- 一般的な概念（search・home・bell・mail など）は公式に存在する可能性が高い。存在する（または存在しそうな）場合は、**公式アイコンの使用を第一候補として案内**する（https://lucide.dev/icons/ で検索できる）
  - Obsidian Iconize なら公式アイコンは `lucide-<名前>` のキーでそのまま使えるため、生成せずに済む
- 案内した上でユーザーがオリジナルを希望する場合のみ生成に進む。その際は**公式と構図・モチーフを意図的にずらす**（要素の追加、視点の変更、別のメタファーなど）。公式の path データをそのまま、またはほぼそのまま出力してはいけない
- ユーザーが**公式アイコンの改変**を明示的に求めた場合は改変してよいが、成果物が Lucide 公式（ISC License）由来であることを伝え、配布時にはライセンス表示が必要になる旨を案内する

公式にない概念（ドメイン固有の概念、複合的な意味など）なら、このチェックは不要でそのまま生成に進んでよい。

---

## 基本テンプレート

すべてのアイコンはこの属性セットを**そのまま**使う。変えてはいけない。

```xml
<svg
  xmlns="http://www.w3.org/2000/svg"
  width="24"
  height="24"
  viewBox="0 0 24 24"
  fill="none"
  stroke="currentColor"
  stroke-width="2"
  stroke-linecap="round"
  stroke-linejoin="round"
>
  <!-- ここに要素を書く -->
</svg>
```

なぜこの形式かというと:
- `fill="none"` + `stroke="currentColor"` により、CSSの `color` プロパティひとつで色が変えられる
- `stroke-linecap="round"` + `stroke-linejoin="round"` により、すべての線端・折れ角が柔らかくなり統一感が生まれる
- 24×24のグリッドは他のLucideアイコンとサイズが揃う

---

## 使える要素と属性

以下の要素と属性**だけ**使う。それ以外はすべてNG。

| 要素 | 使える属性 |
|------|-----------|
| `<path>` | `d` |
| `<line>` | `x1`, `y1`, `x2`, `y2` |
| `<polygon>` | `points` |
| `<polyline>` | `points` |
| `<circle>` | `cx`, `cy`, `r` |
| `<ellipse>` | `cx`, `cy`, `rx`, `ry` |
| `<rect>` | `x`, `y`, `width`, `height`, `rx` |

**禁止事項（理由付き）:**
- `transform` / `filter` — ブラウザ互換性問題・最適化の妨げになる
- 個別の `fill` / `stroke` 属性 — ルートSVGで一括指定するため不要、かつCSSによる色変更を壊す
- `<use>` 要素 — HTML埋め込み時にIDが衝突する
- その他すべてのSVG要素 (`<defs>`, `<g>`, `<symbol>` など)

---

## コーディングルール

- キャンバスは **24×24px** 固定
- パスの数値は**小数点3桁まで**（それ以上は切り捨て）
- インデント: **スペース2つ**
- 要素の属性は1行に書く（改行しない）
- ファイル名は **kebab-case の英小文字**（例: `coffee-cup.svg`）が既定
  - **Obsidian Iconize で使う場合はハイフンを含めない**（例: `coffeecup.svg`）。Iconize がハイフン区切りをPascalCaseに変換するため、含めると表示が不安定になる
  - 既存アイコンパックとの名前衝突を避けたい場合は、短い接尾辞を決めて全ファイルに統一して付けるとよい（ユーザーの指定があればそれに従う）
- **1ファイル = 1アイコン**

---

## デザインルール

- 上下左右に **1px 以上のパディング**を取る（アイコンが端ぎりぎりにならないように）
- 別の要素との間隔は **2px 以上**
- 矩形の角丸 `rx`: 8px以上の矩形 → `rx="2"`、8px未満の小さな矩形 → `rx="1"`
- 複雑な形状は `<path>` で描く
- 滑らかな曲線は arc (`A`) または quadratic curve (`Q`) を優先する

---

## pathコマンド早見表

| コマンド | 意味 |
|---------|------|
| `M x y` | 移動（絶対座標） |
| `m dx dy` | 移動（相対座標） |
| `L x y` | 直線（絶対） |
| `l dx dy` | 直線（相対） |
| `H x` | 水平線 |
| `V y` | 垂直線 |
| `C x1 y1 x2 y2 x y` | 三次ベジェ曲線 |
| `Q x1 y1 x y` | 二次ベジェ曲線 |
| `A rx ry rotation large-arc sweep x y` | 円弧 |
| `Z` | パスを閉じる |

---

## 標準出力フォーマット

アイコンを生成するときは **SVGファイルとJSONファイルを必ずセットで**出力する。
ファイルは `icons/` ディレクトリに並べて置く想定で命名する。

```
icons/
├── my-icon.svg   ← SVGファイル本体
└── my-icon.json  ← メタデータ（contributors・tags・categories）
```

### SVGファイル出力例

`my-icon.svg`:
```xml
<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24"
  fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
  <!-- SVG要素 -->
</svg>
```

### JSONファイルの書き方

`my-icon.json`:
```json
{
  "$schema": "../icon.schema.json",
  "contributors": [
    "github-username"
  ],
  "tags": [
    "keyword1",
    "keyword2"
  ],
  "categories": [
    "devices"
  ]
}
```

**各フィールドの意味:**
- `$schema` — スキーマ参照パス。常に `"../icon.schema.json"` 固定
- `contributors` — アイコン作者のGitHubユーザー名リスト。ユーザーが未指定なら空配列 `[]`
- `tags` — 検索キーワード。アイコンの概念・用途・別名を英語で列挙する（3〜8個が目安）
- `categories` — アイコンが属するカテゴリ。下の一覧から選ぶ

**categoriesに使える値（`icon.schema.json` より）:**

| カテゴリ | 内容 |
|---------|------|
| `accessibility` | アクセシビリティ関連 |
| `account` | アカウント・プロフィール |
| `animals` | 動物 |
| `arrows` | 矢印・方向 |
| `brands` | ブランド・ロゴ |
| `buildings` | 建物・施設 |
| `charts` | グラフ・データ可視化 |
| `communication` | コミュニケーション・メッセージ |
| `connectivity` | ネットワーク・接続 |
| `currency` | 通貨・お金 |
| `cursors` | カーソル・ポインタ |
| `design` | デザイン・UI |
| `development` | 開発・コーディング |
| `devices` | デバイス・ハードウェア |
| `emoji` | 絵文字・感情 |
| `files` | ファイル・ドキュメント |
| `finance` | 金融・ビジネス |
| `food-beverage` | 食べ物・飲み物 |
| `gaming` | ゲーム |
| `home` | 家・インテリア |
| `layout` | レイアウト・UI構造 |
| `mail` | メール |
| `maps` | 地図・場所 |
| `math` | 数学・計算 |
| `media` | メディア・再生 |
| `medical` | 医療・健康 |
| `nature` | 自然・植物 |
| `navigation` | ナビゲーション |
| `notifications` | 通知・アラート |
| `people` | 人・ユーザー |
| `photography` | 写真・カメラ |
| `science` | 科学 |
| `security` | セキュリティ・プライバシー |
| `settings` | 設定 |
| `shapes` | 図形・幾何学 |
| `shopping` | ショッピング |
| `social` | ソーシャル・SNS |
| `sports` | スポーツ |
| `sustainability` | サステナビリティ・エコ |
| `text` | テキスト・タイポグラフィ |
| `time` | 時間・カレンダー |
| `tools` | ツール・ユーティリティ |
| `transportation` | 交通・移動 |
| `travel` | 旅行 |
| `weather` | 天気 |

---

## 出力例（フルセット）

### 検索アイコン（search）

`search.svg`:
```xml
<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24"
  fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
  <path d="m21 21-4.34-4.34" />
  <circle cx="11" cy="11" r="8" />
</svg>
```

`search.json`:
```json
{
  "$schema": "../icon.schema.json",
  "contributors": [],
  "tags": [
    "search",
    "find",
    "magnifier",
    "lookup",
    "query"
  ],
  "categories": [
    "navigation",
    "development"
  ]
}
```

### 家アイコン（house）

`house.svg`:
```xml
<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24"
  fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
  <path d="M15 21v-8a1 1 0 0 0-1-1h-4a1 1 0 0 0-1 1v8" />
  <path d="M3 10a2 2 0 0 1 .709-1.528l7-6a2 2 0 0 1 2.582 0l7 6A2 2 0 0 1 21 10v9a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2z" />
</svg>
```

`house.json`:
```json
{
  "$schema": "../icon.schema.json",
  "contributors": [],
  "tags": [
    "home",
    "house",
    "building",
    "residence",
    "dwelling"
  ],
  "categories": [
    "home",
    "buildings",
    "navigation"
  ]
}
```

※ 上記2つのアイコンのpathデータは [Lucide](https://lucide.dev) 公式アイコン（ISC License）を出力例として引用したもの。

---

## チェックリスト（生成後に確認する）

- [ ] 公式 Lucide に同じ概念のアイコンがないか確認したか（ある場合は公式を案内したか）
- [ ] 公式アイコンの path をそのまま（またはほぼそのまま）出力していないか
- [ ] キャンバスは 24×24px か
- [ ] 上下左右に 1px 以上のパディングがあるか
- [ ] `stroke-width` は 2px か
- [ ] `stroke-linecap` / `stroke-linejoin` は `round` か
- [ ] 矩形の `rx` は適切か（8px以上→2、8px未満→1）
- [ ] 別の要素との間隔は 2px 以上あるか
- [ ] 禁止要素・属性を使っていないか
- [ ] ファイル名は命名規則に沿っているか（既定: kebab-case 英小文字。Iconize用ならハイフンなし）
- [ ] JSONファイルをSVGとセットで出力したか
- [ ] `tags` に検索で使いやすい英語キーワードを入れたか
- [ ] `categories` は上の一覧から正しい値を選んでいるか
