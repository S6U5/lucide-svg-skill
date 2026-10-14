# lucide-svg — Agent Skill

[English](./README.md) | **日本語**

[Agent Skills](https://agentskills.io) 標準のスキル。[Lucide](https://lucide.dev) の設計言語（24×24 グリッド・`stroke-width: 2`・丸い線端）に沿ったアイコン SVG を、メタデータ JSON とセットで生成します。**Claude Code・Codex・Cursor・Gemini CLI・GitHub Copilot** など、Agent Skills 対応ツールならどれでも使えます。

**本スキルは Lucide 公式プロジェクトとは無関係の非公式ツールです。**

## できること

- Lucide の視覚言語に一貫して従ったオリジナルアイコンの新規作成
- 既存アイコンの改変（要素の追加・調整）
- Lucide 公式リポジトリと同形式のメタデータ JSON（`contributors` / `use-cases` / `tags` / `categories`）の同時生成
- 生成後のセルフチェック（パディング・禁止要素・命名規則）

デザイン上の制約（使える SVG 要素の制限・禁止事項とその理由・角丸の使い分けなど）をスキル内に明文化してあるため、複数のアイコンを別々の会話で作っても見た目が揃います。

## 活用例

- **Obsidian Iconize のカスタムアイコンパック作り** — vault のフォルダ用に、標準の Lucide アイコンと並べても浮かないオリジナルアイコンを量産する（下記の注意点あり）
- **Web アプリ・モバイルアプリの独自アイコン** — `lucide-react` などで Lucide を使っているプロジェクトで、公式セットにないドメイン固有のアイコン（自社サービスの概念・業務用語など）を同じトーンで補完する
- **Lucide 本家に存在しないアイコンの穴埋め** — 「あと 1 個だけ欲しいのに無い」ときに、別のアイコンセットを混ぜて統一感を壊さずに済む
- **アイコン提案のたたき台** — use-cases / tags / categories 付きの JSON がセットで出るので、デザイナーへの依頼やチーム内レビューの下書きとして使える

## インストール

このリポジトリを `lucide-svg` という名前で、使うエージェントのスキルディレクトリに置くだけです。

```sh
# Claude Code — ユーザー全体
git clone https://github.com/S6U5/lucide-svg-skill.git ~/.claude/skills/lucide-svg

# Claude Code — 特定プロジェクトのみ
git clone https://github.com/S6U5/lucide-svg-skill.git <project>/.claude/skills/lucide-svg

# Codex（および共有 ~/.agents ディレクトリを読むツール）— ユーザー全体
git clone https://github.com/S6U5/lucide-svg-skill.git ~/.agents/skills/lucide-svg

# Codex — 特定プロジェクトのみ
git clone https://github.com/S6U5/lucide-svg-skill.git <project>/.agents/skills/lucide-svg
```

その他の Agent Skills 対応ツール（Cursor・Gemini CLI など）は、各ツールのスキルディレクトリに clone してください。

## 使い方

エージェントにそのまま話しかけると発火します:

- 「コーヒーカップのアイコンを Lucide スタイルで作って」
- 「鍵マークのアイコン、svg と json のセットで」
- 「この bookmark アイコンを改造して星付きにして」

イラスト・ロゴ・図解など、アイコン以外の SVG 依頼では発火しないよう description を調整済みです。

出力フォーマットの実例（SVG + JSON のフルセット）は [SKILL.md の「出力例」セクション](./SKILL.md#出力例フルセット)を参照してください。

## Obsidian Iconize で使う場合

生成したアイコンは [Iconize](https://github.com/FlorianWoelki/obsidian-iconize) のカスタムアイコンパックとしてそのまま使えます。1点だけ注意:

- **ファイル名にハイフンを入れない**こと（Iconize がハイフン区切りを PascalCase に変換するため表示が不安定になります）。スキルはこのルールを把握しており、Iconize 用と伝えればハイフンなしの名前で出力します。

### アイコンパック配置の Tips

Iconize のカスタムアイコンパックは `.obsidian/icons/<パック名>.zip` に置きますが、運用でハマりやすいポイントがいくつかあります（パック名を `mypack`、ソースを `icons/mypack/` と仮定）:

1. **zip は毎回削除して完全再作成する。** `zip -r` は追記モードなので、削除したアイコンが古い zip に残り続けます。

   ```sh
   rm -f .obsidian/icons/mypack.zip
   cd icons && COPYFILE_DISABLE=1 zip -r ../.obsidian/icons/mypack.zip mypack/ -x "*.DS_Store"
   ```

   macOS では `COPYFILE_DISABLE=1` を付けないと `__MACOSX/` ディレクトリが zip に混入します。

2. **zip 更新だけでは反映されない。** Iconize は展開済みディレクトリ（`.obsidian/icons/mypack/`）のファイルを直接参照するため、zip 更新後に同期が必要です。

   ```sh
   unzip -o .obsidian/icons/mypack.zip -d .obsidian/icons/
   ```

3. **iCloud 同期の vault では重複ファイルに注意。** 同期衝突で生まれる `xxx 2.svg` のようなファイルが展開済みディレクトリに残ると、Iconize が同一アイコン名に複数ファイルを検出して表示が壊れます。見つけたら削除してください。

4. **反映には Obsidian の再起動**（またはプラグインの再読み込み）が必要です。

## リポジトリ構成

```
SKILL.md      ← スキル本体（デザインルール・出力フォーマット・チェックリスト）
evals/        ← スキルの動作検証用 eval 定義
LICENSE       ← スキル本体のライセンス（MIT）
LICENSE-lucide ← SKILL.md 内で引用している Lucide 公式 path データのライセンス（ISC）
```

## ライセンス

- スキル本体: [MIT License](./LICENSE)
- SKILL.md 内の出力例 2 点（search / house）の path データは [Lucide](https://lucide.dev) 公式アイコンからの引用です（[ISC License](./LICENSE-lucide) — Copyright (c) Lucide Icons and Contributors）。`search` は Lucide が Feather から引き継いだアイコンのため、同じファイルに収めた MIT License（Copyright (c) Cole Bemis）の対象でもあります。
