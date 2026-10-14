# lucide-svg — Agent Skill

**English** | [日本語](./README.ja.md)

An [Agent Skill](https://agentskills.io) for generating **original [Lucide](https://lucide.dev)-style icon SVGs** (24×24 grid, `stroke-width: 2`, rounded caps) together with Lucide-format metadata JSON (`contributors` / `use-cases` / `tags` / `categories`). Works with **Claude Code, Codex, Cursor, Gemini CLI, GitHub Copilot**, and any other tool that supports the Agent Skills standard.

Unlike icon-search skills, this one *creates new icons* that visually match the Lucide icon set — useful for custom icon packs (e.g. Obsidian Iconize) and app-specific icons Lucide doesn't ship.

The skill instructions are written in Japanese, but this does not limit usage: agents read them natively, so you can talk to your agent in English (or any language) and the skill works the same. The generated SVGs and JSON are language-neutral.

**This is an unofficial tool, not affiliated with the Lucide project.**

## Features

- Creates original icons that consistently follow Lucide's visual language
- Modifies existing icons (adding or adjusting elements)
- Generates metadata JSON in the same format as the official Lucide repository (`contributors` / `use-cases` / `tags` / `categories`)
- Self-checks after generation (padding, forbidden elements, naming rules)
- Recommends the official Lucide icon first when the requested concept already exists, so you don't reinvent (or accidentally reproduce) an official icon

The design constraints (allowed SVG elements, forbidden features with reasons, corner-radius rules, etc.) are spelled out in the skill, so icons created in separate conversations still look like one set.

## Use cases

- **Building a custom icon pack for Obsidian Iconize** — mass-produce original folder icons that sit naturally next to the built-in Lucide icons (see the note below)
- **App-specific icons** — in a project using `lucide-react` etc., fill in domain-specific concepts the official set doesn't cover, in the same visual tone
- **Filling gaps in the official set** — when you need "just one more icon", avoid mixing in another icon set and breaking visual consistency
- **Drafts for design requests** — the paired JSON (use cases / tags / categories) makes a handy starting point for a designer hand-off or team review

## Installation

Clone this repository into your agent's skills directory under the name `lucide-svg`:

```sh
# Claude Code — user-level
git clone https://github.com/S6U5/lucide-svg-skill.git ~/.claude/skills/lucide-svg

# Claude Code — single project
git clone https://github.com/S6U5/lucide-svg-skill.git <project>/.claude/skills/lucide-svg

# Codex (and other tools reading the shared ~/.agents directory) — user-level
git clone https://github.com/S6U5/lucide-svg-skill.git ~/.agents/skills/lucide-svg

# Codex — single project
git clone https://github.com/S6U5/lucide-svg-skill.git <project>/.agents/skills/lucide-svg
```

For other Agent Skills-compatible tools (Cursor, Gemini CLI, etc.), clone into that tool's skills directory — check its documentation.

## Usage

Just talk to your agent naturally and the skill triggers:

- "Create a coffee cup icon in Lucide style"
- "A lock icon please, as an svg + json set"
- "Modify this bookmark icon to add a star"

The skill's `description` is tuned so it does *not* trigger for non-icon SVG requests (illustrations, logos, diagrams, banners).

For a full example of the output format (SVG + JSON set), see the [examples section in SKILL.md](./SKILL.md#出力例フルセット).

## Using with Obsidian Iconize

Generated icons work as-is in an [Iconize](https://github.com/FlorianWoelki/obsidian-iconize) custom icon pack. One caveat:

- **Do not use hyphens in file names** (Iconize converts hyphen-separated names to PascalCase, which makes icon display unstable). The skill knows this rule — tell it the icons are for Iconize and it outputs hyphen-free names.

### Icon pack deployment tips

Iconize custom icon packs live at `.obsidian/icons/<pack>.zip`. A few pitfalls worth knowing (assuming the pack is `mypack` and sources are in `icons/mypack/`):

1. **Delete and fully rebuild the zip every time.** `zip -r` appends, so deleted icons linger in an old zip.

   ```sh
   rm -f .obsidian/icons/mypack.zip
   cd icons && COPYFILE_DISABLE=1 zip -r ../.obsidian/icons/mypack.zip mypack/ -x "*.DS_Store"
   ```

   On macOS, `COPYFILE_DISABLE=1` keeps the `__MACOSX/` directory out of the zip.

2. **Updating the zip alone is not enough.** Iconize reads files directly from the extracted directory (`.obsidian/icons/mypack/`), so sync it after updating the zip:

   ```sh
   unzip -o .obsidian/icons/mypack.zip -d .obsidian/icons/
   ```

3. **Watch for duplicate files in iCloud-synced vaults.** Sync conflicts create files like `xxx 2.svg`; if they remain in the extracted directory, Iconize detects multiple files for one icon name and display breaks. Delete them when found.

4. **Restart Obsidian** (or reload the plugin) for changes to take effect.

## Repository layout

```
SKILL.md       ← the skill itself (design rules, output format, checklist)
evals/         ← eval definitions for testing the skill
LICENSE        ← license for the skill itself (MIT)
LICENSE-lucide ← license for the official Lucide path data quoted in SKILL.md (ISC)
```

## License

- The skill itself: [MIT License](./LICENSE)
- The path data of the two output examples in SKILL.md (search / house) is quoted from official [Lucide](https://lucide.dev) icons ([ISC License](./LICENSE-lucide) — Copyright (c) Lucide Icons and Contributors). `search` is one of the icons Lucide derives from Feather, so it is also covered by the MIT License (Copyright (c) Cole Bemis) included in the same file.
