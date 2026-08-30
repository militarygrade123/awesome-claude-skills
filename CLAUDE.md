# CLAUDE.md

Guidance for Claude Code (and other AI assistants) working in this repository.

## What This Repository Is

`awesome-claude-skills` is a **curated content repository**, not an application. It contains:

1. An "awesome list" (`README.md`) linking to Claude Skills — both ones vendored in this repo and ones hosted elsewhere on GitHub.
2. ~27 skill directories, each holding a `SKILL.md` plus optional supporting assets.
3. A Claude Code plugin marketplace manifest (`.claude-plugin/marketplace.json`) that exposes the local skills as installable plugins.

There is **no build system, no test suite, no CI, no package manifest, and no linter**. Do not add `npm install`, `pytest`, or CI-shaped scaffolding unless explicitly asked. The deliverable of nearly every task here is Markdown plus occasional helper scripts.

Licensed Apache-2.0 at the repo level; individual skills carry their own `LICENSE.txt` where applicable (the Anthropic-authored ones are proprietary — see below).

## Repository Layout

```
.
├── README.md                     # The awesome list. Source of truth for discovery.
├── CONTRIBUTING.md               # Contributor-facing rules + the canonical SKILL.md template.
├── .claude-plugin/
│   └── marketplace.json          # Plugin marketplace manifest (one entry per local skill).
├── <skill-name>/                 # One directory per skill, hyphen-case.
│   ├── SKILL.md                  # REQUIRED. YAML frontmatter + instructions.
│   ├── LICENSE.txt               # Present on Anthropic-derived skills.
│   ├── scripts/                  # Optional helper scripts (Python / bash / JS).
│   ├── templates/                # Optional templates.
│   ├── examples/ or reference/   # Optional reference material.
│   └── assets (fonts, themes)    # Optional binary/asset payloads.
└── document-skills/              # Nested group: docx/, pdf/, pptx/, xlsx/ — each a full skill.
```

Skills fall into two rough buckets:

- **Simple skills** — a single `SKILL.md` of prose instructions (`changelog-generator`, `file-organizer`, `invoice-organizer`, `video-downloader`, `raffle-winner-picker`, `image-enhancer`, `lead-research-assistant`, `domain-name-brainstormer`, `competitive-ads-extractor`, `content-research-writer`, `meeting-insights-analyzer`, `developer-growth-analysis`, `skill-share`, `template-skill`).
- **Bundled skills** — `SKILL.md` plus executable/asset payloads that the skill's instructions invoke:
  - `document-skills/{docx,pptx}` — Python scripts under `scripts/` and `ooxml/scripts/`, plus large vendored OOXML `.xsd` schema trees.
  - `document-skills/pdf` — Python form-filling and bounding-box scripts.
  - `document-skills/xlsx` — `recalc.py`.
  - `slack-gif-creator` — `core/` animation primitives + `templates/` per-effect modules + `requirements.txt` (pillow, imageio, imageio-ffmpeg, numpy).
  - `mcp-builder` — `reference/` guides for Python/Node MCP servers + `scripts/` evaluation harness (`requirements.txt`: anthropic, mcp).
  - `webapp-testing` — Playwright `examples/` and `scripts/with_server.py`.
  - `artifacts-builder` — bash `scripts/init-artifact.sh`, `scripts/bundle-artifact.sh`, and a `shadcn-components.tar.gz` payload.
  - `skill-creator` — `scripts/init_skill.py`, `package_skill.py`, `quick_validate.py`.
  - `canvas-design` — `canvas-fonts/` (TTFs + OFL license files).
  - `theme-factory` — `themes/*.md` (10 presets) + `theme-showcase.pdf`.
  - `internal-comms` — `examples/*.md` writing samples.

## SKILL.md Anatomy

Every skill starts with YAML frontmatter:

```markdown
---
name: skill-name
description: What the skill does AND when Claude should use it.
license: Complete terms in LICENSE.txt   # only on skills that ship a LICENSE.txt
---

# Skill Name
...
```

Frontmatter rules (enforced by `skill-creator/scripts/quick_validate.py`):

- `name` and `description` are both **required**.
- `name` must be hyphen-case: `^[a-z0-9-]+$`, no leading/trailing hyphen, no `--`, and **must match the directory name exactly**. `init_skill.py` additionally documents a 40-character cap.
- `description` must not contain `<` or `>`. Wrap in double quotes if it contains a colon (see `document-skills/*/SKILL.md`).
- The description is what triggers skill activation — write it as "does X; use when Y", not just "does X".

Body conventions (from `CONTRIBUTING.md` and the existing corpus):

- `# Skill Name` H1, then `## When to Use This Skill`, `## What This Skill Does`, `## How to Use` (with `### Basic Usage` / `### Advanced Usage`), `## Example`, `## Tips`, `## Common Use Cases`.
- Write instructions **for Claude**, not for end users.
- Include an attribution line where applicable: `**Inspired by:** [Person]'s workflow` or `**Credit:** Based on [Team]'s process`.
- Community skills range from ~100 to ~650 lines; length should follow the complexity of the workflow, not a target.

## Adding or Changing a Skill: The Three-File Rule

A local skill is only fully wired up when **all three** of these are updated:

1. `<skill-name>/SKILL.md` — the skill itself, in a new hyphen-case directory.
2. `README.md` — one bullet in the right category section, alphabetical within the category:
   ```markdown
   - [Skill Name](./skill-name/) - One-sentence description. Inspired by [Person/Source].
   ```
   No emojis; match surrounding punctuation exactly.
3. `.claude-plugin/marketplace.json` — an entry in `plugins`:
   ```json
   {
     "name": "skill-name",
     "description": "One-sentence description.",
     "source": "./skill-name",
     "category": "development"
   }
   ```

Note the two category vocabularies do **not** match and both must be respected:

- `marketplace.json` `category` values are slugs: `business-marketing`, `communication-writing`, `creative-media`, `development`, `productivity-organization`.
- `README.md` section headings are broader and prose-cased: Document Processing, Development & Code Tools, Data & Analysis, Business & Marketing, Communication & Writing, Creative & Media, Productivity & Organization, Collaboration & Project Management, Security & Systems.
- Nested skills get a flattened marketplace name: `document-skills/docx` → `"name": "document-skills-docx"`, `"source": "./document-skills/docx"`.
- README bullets for **external** skills link to the upstream GitHub URL and append `*By [@handle](...)*`; bullets for **local** skills use a relative `./skill-name/` link.

## Validation

There is no CI, so validate manually before committing:

```bash
# Frontmatter / naming validation for a single skill
python3 skill-creator/scripts/quick_validate.py <skill-directory>

# Scaffold a new skill directory (creates SKILL.md + scripts/ references/ assets/)
python3 skill-creator/scripts/init_skill.py <skill-name> --path <parent-directory>

# Package a skill into a distributable zip
python3 skill-creator/scripts/package_skill.py <path/to/skill-folder> [output-dir]

# JSON syntax check on the marketplace manifest
python3 -m json.tool .claude-plugin/marketplace.json > /dev/null
```

A useful consistency sweep after any skill addition — every local `SKILL.md` directory should have a matching `marketplace.json` `source`, and every `name:` should equal its directory basename:

```bash
python3 - <<'PY'
import json, os, re
srcs = {p['source'].lstrip('./') for p in json.load(open('.claude-plugin/marketplace.json'))['plugins']}
dirs = {r.lstrip('./') for r, _, f in os.walk('.') if 'SKILL.md' in f and '.git' not in r}
print('marketplace sources with no directory:', sorted(s for s in srcs if not os.path.isdir(s)))
print('skill directories missing from marketplace:', sorted(dirs - srcs))
for d in sorted(dirs):
    n = re.search(r'^name:\s*(.+)$', open(os.path.join(d, 'SKILL.md')).read(), re.M).group(1).strip()
    if n != os.path.basename(d):
        print('name/dir mismatch:', d, '->', n)
PY
```

## Known Inconsistencies (as of this writing)

Do not "fix" these silently as a side effect of unrelated work — they are pre-existing and each is a judgment call for the maintainers:

- `marketplace.json` lists `algorithmic-art` (`"source": "./algorithmic-art"`) but **that directory does not exist** in the repo.
- `skill-share/` exists with a valid `SKILL.md` but has **no `marketplace.json` entry** and no README bullet.
- `template-skill/` and `developer-growth-analysis/` are in `marketplace.json` but not linked from `README.md`.
- `artifacts-builder/` and `document-skills/{docx,pdf,pptx,xlsx}/` are vendored locally but `README.md` links them to `github.com/anthropics/skills` rather than to the local copies.
- `README.md`'s API example pins `claude-3-5-sonnet-20241022`, which is stale relative to current models.

If a task's scope covers one of these, fix it and say so explicitly.

## Working Conventions

- **Vendored content is off-limits for stylistic edits.** `document-skills/*/ooxml/schemas/**/*.xsd`, `canvas-design/canvas-fonts/*`, `artifacts-builder/scripts/shadcn-components.tar.gz`, and `theme-factory/theme-showcase.pdf` are upstream/binary payloads. Never reformat, regenerate, or "clean up" them.
- **Anthropic-derived skills carry proprietary licenses.** `document-skills/*`, `artifacts-builder`, `brand-guidelines`, `canvas-design`, `internal-comms`, `mcp-builder`, `skill-creator`, `slack-gif-creator`, `theme-factory`, and `webapp-testing` ship a `LICENSE.txt` and their frontmatter references it. Preserve both the file and the `license:` frontmatter line when editing.
- **Python helper scripts** in this repo are stdlib-plus-a-few-deps, invoked as `python3 path/to/script.py <args>` with `sys.argv` parsing and emoji-prefixed status output (`🚀`, `✅`, `❌`). Match that style when extending them; per-skill dependencies belong in that skill's own `requirements.txt`, never in a repo-root one.
- **Bash scripts** (`artifacts-builder/scripts/*.sh`) use `set -e` and branch on Node version and `$OSTYPE` for macOS/Linux `sed` differences. Keep them portable across both.
- **Skill quality bar** (`CONTRIBUTING.md`): based on a real use case, well documented, accessible to non-technical users, includes examples, tested across Claude.ai / Claude Code / API, confirms before destructive operations, portable across platforms.

## Git Workflow

- Default branch is `master`.
- Branch naming for contributions: `add-skill-name` (per `CONTRIBUTING.md`); commits read `Add [Skill Name] skill` or conventional-commit style (`feat: add ... skill`).
- PR titles: `Add [Skill Name] skill`, with a body covering the problem solved, who uses the workflow, attribution, and a usage example.
- Do not open a pull request unless explicitly asked.

## Installing These Skills Locally

For Claude Code, a skill directory is copied into the skills path and picked up on next launch:

```bash
mkdir -p ~/.config/claude-code/skills/
cp -r <skill-name> ~/.config/claude-code/skills/
head ~/.config/claude-code/skills/<skill-name>/SKILL.md   # verify frontmatter
```

Alternatively the whole repo can be added as a Claude Code plugin marketplace via `.claude-plugin/marketplace.json`.
