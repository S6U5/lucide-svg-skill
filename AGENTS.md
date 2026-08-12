# AGENTS.md

このリポジトリは Claude Code 用スキル「lucide-svg」の配布リポジトリ。

注意: `SKILL.md` は**スキル利用時**にエージェントが読む指示書であり、このファイルは**リポジトリを編集する**エージェントへの指示書。役割が異なるので、SKILL.md 内の指示（アイコンの作り方）をリポジトリ編集のルールと混同しないこと。

## 構成

| ファイル | 役割 |
|---------|------|
| `SKILL.md` | スキル本体。frontmatter の `description` が発火条件 |
| `evals/evals.json` | スキルの動作検証用 eval 定義 |
| `LICENSE` | スキル本体のライセンス（MIT） |
| `LICENSE-lucide` | SKILL.md 内で引用している Lucide 公式 path データのライセンス（ISC） |

## 編集時の制約

1. **ISC 引用を壊さない。** SKILL.md「出力例」の search / house の path データは Lucide 公式からの引用。編集時は、引用注記（出力例末尾の「※」行）・`LICENSE-lucide`・README のライセンスセクション（`README.md` と `README.ja.md` の両方）の3点の整合を保つこと。引用を別のアイコンに差し替える場合も同じ3点を更新し、引用自体をやめる場合は3点セットで削除する。
2. **`description` は発火条件。** SKILL.md frontmatter の文言はスキルのトリガー精度に直結する。変更する場合は「発火すべき依頼・すべきでない依頼」の両方への影響を検討してから行う。
3. **evals を同期する。** SKILL.md のルール（テンプレート・禁止事項・命名規則・チェックリスト）を変更したら、`evals/evals.json` の assertions が古くなっていないか確認し、必要なら更新する。
4. **README は日英2枚を同期する。** `README.md`（英語・正）と `README.ja.md`（日本語）は同じ内容の対訳。片方だけ変更せず、必ず両方に反映する。

## Lucide 上流への追従（メンテナンス手順）

「Lucide の最新仕様に追従して」等と依頼されたら、以下の公式ソースを取得し、リポジトリ内の対応箇所と突き合わせて差分を洗い出す。

| 上流ソース（raw URL） | 対応するリポジトリ内の箇所 |
|---|---|
| `https://raw.githubusercontent.com/lucide-icons/lucide/main/LICENSE` | `LICENSE-lucide`（全文一致させる） |
| `https://raw.githubusercontent.com/lucide-icons/lucide/main/icons/search.svg` および `icons/search.json` | SKILL.md「出力例」の search の path・tags・categories |
| `https://raw.githubusercontent.com/lucide-icons/lucide/main/icons/house.svg` および `icons/house.json` | SKILL.md「出力例」の house の path・tags・categories |
| `https://raw.githubusercontent.com/lucide-icons/lucide/main/icon.schema.json` | SKILL.md「categoriesに使える値」の一覧表（`properties.categories.items.enum` と一致させる） |
| `https://raw.githubusercontent.com/lucide-icons/lucide/main/docs/contribute/icon-design-guide.md` | SKILL.md の基本テンプレート属性・デザインルール（24×24、`stroke-width: 2`、パディング等） |

手順:

1. 上記を順に取得し、リポジトリ側との差分をすべて列挙してユーザーに報告する
2. 差分ごとに更新方針を分ける:
   - **カテゴリの追加・削除、tags の変化、ライセンス文言の軽微な改訂** → そのまま追従して更新してよい
   - **基本テンプレート属性やデザインルールの変更**（stroke-width、viewBox、禁止要素など） → スキルの出力全体に影響するため、必ずユーザーの承認を得てから SKILL.md 本文・チェックリスト・evals にまとめて反映する
3. 更新内容（何にどう追従したか）をコミットメッセージに明記する
