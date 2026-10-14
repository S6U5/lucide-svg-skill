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

Lucide の設計ルールに従って、公式アイコンと並べても違和感のない線画アイコンを作る。

**アイコンを生成するときは、SVG と JSON メタデータを必ずセットで出力する。**
ユーザーが「SVGだけでいい」と明示した場合のみ JSON を省略する。

---

## 生成前の確認: 公式アイコンとの重複チェック

作る前に、**依頼された概念が公式 Lucide に既に存在しないか**を考える。

- 一般的な概念（search・house・bell・mail など）は公式にある可能性が高い。ある（またはありそうな）場合は、**公式アイコンの使用を第一候補として案内**する（https://lucide.dev/icons/ で検索できる）
  - Obsidian Iconize なら公式アイコンは `lucide-<名前>` のキーでそのまま使えるため、生成せずに済む
- 案内した上でユーザーがオリジナルを希望する場合のみ生成に進む。その際は**公式と構図・モチーフを意図的にずらす**（要素の追加、視点の変更、別のメタファーなど）。公式の path データをそのまま、またはほぼそのまま出力してはいけない
- ユーザーが**公式アイコンの改変**を明示的に求めた場合は改変してよいが、成果物が Lucide 公式（ISC License。Feather 由来のアイコンは MIT License も）に由来することを伝え、配布時にはライセンス表示が必要になる旨を案内する

公式にない概念（ドメイン固有の概念、複合的な意味など）なら、このチェックは不要でそのまま生成に進んでよい。

---

## 基本テンプレート

ルート要素は、属性・順序・改行まで**この形のまま**使う（Lucide 公式の SVG と同じ書式）。

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

この形式の理由:
- `fill="none"` + `stroke="currentColor"` により、CSS の `color` ひとつで色が変えられる
- `stroke-linecap="round"` + `stroke-linejoin="round"` により、線端と折れ角が柔らかくそろう
- 24×24 のグリッドで、他の Lucide アイコンとサイズがそろう

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
- パスの数値は**小数点以下3桁まで**に丸める
- インデント: **スペース2つ**
- 子要素の属性は1行に書く（改行しない）。自己終了タグは ` />` で閉じる
- ファイル名は **kebab-case の英小文字**（例: `coffee-cup.svg`）が既定。SVG と JSON は同じ名前にする
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

## 出力フォーマット

SVG と JSON を同じ名前で `icons/` に並べて置く想定で出力する。

```
icons/
├── my-icon.svg   ← SVG本体
└── my-icon.json  ← メタデータ
```

### JSON メタデータ

Lucide 公式の `icon.schema.json` に通る形で書く。次の5つはすべて必須。

```json
{
  "$schema": "../icon.schema.json",
  "contributors": [],
  "use-cases": [
    "Representing example items in an interface"
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

- `$schema` — 常に `"../icon.schema.json"`
- `contributors` — 作者の GitHub ユーザー名。ユーザーが未指定なら空配列 `[]`。公式アイコンを改変した場合は、元アイコンの contributors も含める
- `use-cases` — UI のどんな場面で使うかを英語の短い句で 1〜3 個。`Representing` / `Indicating` / `Marking` / `Showing` / `Toggling` などの -ing 動詞で始め、句点や Markdown は付けない。アイコン名や見た目の説明ではなく、UI での役割を書く（例: `Indicating a device is offline or unreachable`）
- `tags` — 検索キーワード。概念・同義語・関連語を英語の小文字で 3〜10 個が目安。**アイコン名そのものと `icon` のような汎用語は入れない**
- `categories` — 下の一覧から選ぶ。一覧にない値は作らない

**categories に使える値（`icon.schema.json` の enum）:**

| カテゴリ | 内容 |
|---------|------|
| `accessibility` | アクセシビリティ |
| `account` | アカウント・プロフィール |
| `animals` | 動物 |
| `arrows` | 矢印・方向 |
| `buildings` | 建物・施設 |
| `charts` | グラフ・データ可視化 |
| `communication` | コミュニケーション・メッセージ |
| `connectivity` | ネットワーク・接続 |
| `cursors` | カーソル・ポインタ |
| `design` | デザイン・UI |
| `development` | 開発・コーディング |
| `devices` | デバイス・ハードウェア |
| `emoji` | 絵文字・感情 |
| `files` | ファイル・ドキュメント |
| `finance` | 金融・通貨・ビジネス |
| `food-beverage` | 食べ物・飲み物 |
| `gaming` | ゲーム |
| `home` | 家・インテリア |
| `layout` | レイアウト・UI構造 |
| `mail` | メール |
| `math` | 数学・計算 |
| `medical` | 医療・健康 |
| `multimedia` | 音楽・動画・再生 |
| `nature` | 自然・植物 |
| `navigation` | ナビゲーション・地図 |
| `notifications` | 通知・アラート |
| `people` | 人・ユーザー |
| `photography` | 写真・カメラ |
| `science` | 科学 |
| `seasons` | 季節 |
| `security` | セキュリティ・プライバシー |
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
  <path d="m21 21-4.34-4.34" />
  <circle cx="11" cy="11" r="8" />
</svg>
```

`search.json`:
```json
{
  "$schema": "../icon.schema.json",
  "contributors": [],
  "use-cases": [
    "Representing search fields and actions"
  ],
  "tags": [
    "find",
    "scan",
    "magnifier",
    "magnifying glass",
    "lens",
    "locate",
    "explore",
    "discover",
    "enlarge",
    "zoom"
  ],
  "categories": [
    "text",
    "social"
  ]
}
```

### 家アイコン（house）

`house.svg`:
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
  <path d="M15 21v-8a1 1 0 0 0-1-1h-4a1 1 0 0 0-1 1v8" />
  <path d="M3 10a2 2 0 0 1 .709-1.528l7-6a2 2 0 0 1 2.582 0l7 6A2 2 0 0 1 21 10v9a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2z" />
</svg>
```

`house.json`:
```json
{
  "$schema": "../icon.schema.json",
  "contributors": [],
  "use-cases": [
    "Navigating to the home page or start screen"
  ],
  "tags": [
    "home",
    "living",
    "building",
    "residence",
    "architecture"
  ],
  "categories": [
    "buildings",
    "home",
    "navigation"
  ]
}
```

※ 上記2つのアイコンのpathデータは [Lucide](https://lucide.dev) 公式アイコン（ISC License）を出力例として引用したもの。search は Feather 由来のため MIT License（Cole Bemis）の対象でもある。全文は `LICENSE-lucide`。tags・categories は公式に合わせ、contributors は「ユーザー未指定なら空配列」の例として空にしている。use-cases は公式が空のため、書き方の例としてこのスキルで書いたもの。

---

## チェックリスト（生成後に確認する）

- [ ] 公式 Lucide に同じ概念のアイコンがないか確認したか（ある場合は公式を案内したか）
- [ ] 公式アイコンの path をそのまま（またはほぼそのまま）出力していないか
- [ ] ルート要素は基本テンプレートのままか（24×24、`stroke-width="2"`、`round`）
- [ ] 上下左右に 1px 以上のパディングがあるか
- [ ] 矩形の `rx` は適切か（8px以上→2、8px未満→1）
- [ ] 別の要素との間隔は 2px 以上あるか
- [ ] 禁止要素・属性を使っていないか
- [ ] ファイル名は命名規則に沿っているか（既定: kebab-case 英小文字。Iconize用ならハイフンなし）。SVG と JSON は同じ名前か
- [ ] JSON を SVG とセットで出力したか
- [ ] JSON に `$schema`・`contributors`・`use-cases`・`tags`・`categories` がすべてあるか
- [ ] `tags` は英語の小文字で、アイコン名そのものを含んでいないか
- [ ] `categories` は上の一覧から選んでいるか
