# CollectiveAI Skills

Agent skills encoding our engineering standards. The rules are derived from real production
practice across our Python, FastAPI, Prefect, LLM/AI, ASR, and Next.js codebases, plus Matt
Pocock's deep-module skills — not from generic defaults. They are written to be self-contained
and usable by anybody.

## Install

Install with the [`skills`](https://skills.sh) CLI — it clones this repo and drops the skills
into your agent (Claude Code, Codex, Cursor, …):

```bash
# add both skills to the current project
npx skills@latest add collectiveai-team/skills

# or pick one
npx skills@latest add collectiveai-team/skills --skill collective-pr-review

# preview what's in the repo without installing
npx skills@latest add collectiveai-team/skills --list
```

Useful flags: `-g` install globally (user-level), `--copy` copy files instead of symlinking,
`-a <agent>` target a specific agent (`claude-code`, `codex`, `cursor`, …), `--all` install
everything non-interactively.

Each skill installs self-contained — it carries its own `rules/` and compiled `AGENTS.md`, so
no extra setup is needed.

## Usage

Once installed, the skills are invoked by your agent:

- **`collective-refactor`** — adapt an existing codebase to our style and architecture. Trigger
  it by asking the agent to "refactor toward CollectiveAI style", modernize a project, or clean
  up architecture.
- **`collective-pr-review`** — a PR gate. Runs before the agent creates, updates, or marks a PR
  ready: applies the shared rules plus deep-module and large-file/spaghetti checks.

Both read `rules/_sections.md` (the index) and open the specific `rules/<id>.md` they need.

## How rules are structured

Rules are **atomic** — one rule per file under `rules/` — following the pattern Vercel uses for
its engineering skills. The canonical source lives once at the repo root; a build step copies it
into each skill so every skill installs self-contained.

```text
.
├── rules/                       # SOURCE OF TRUTH — edit here
│   ├── _sections.md             # the index: every rule, grouped by section, with its id
│   ├── _template.md             # skeleton for authoring a new rule
│   ├── general-*.md  py-*.md  pylayout-*.md  arch-*.md  spaghetti-*.md
│   ├── test-*.md  log-*.md  api-*.md  llm-*.md  prefect-*.md  asr-*.md
│   └── fe-*.md  infra-*.md  k8s-*.md  pr-*.md
├── scripts/
│   └── build_skills.py          # compiles rules/ into each skill bundle + AGENTS.md
└── skills/engineering/
    ├── collective-refactor/
    │   ├── SKILL.md             # workflow + index pointing at rules/<id>.md
    │   ├── rules/               # GENERATED bundle (do not edit)
    │   └── AGENTS.md            # GENERATED compiled ruleset (do not edit)
    └── collective-pr-review/
        ├── SKILL.md
        ├── rules/               # GENERATED bundle
        └── AGENTS.md            # GENERATED
```

Each rule carries frontmatter:
- `applies-to` — `all`, or a comma list of stack tags (`python`, `fastapi`, `prefect`, `asr`,
  `llm`, `nextjs`, `k8s`, `edge`). This scopes a rule so stack-specific guidance only fires where
  it belongs.
- `status` — `current` (the standard) or `direction` (where existing code should head).
- `scope`, `section`, `title`, `tags`.

A skill references rules by reading `rules/_sections.md` (the index), then opening the specific
`rules/<id>.md` it needs (progressive disclosure). `AGENTS.md` is every rule compiled into one
document for whole-ruleset reads.

## Editing the rules

1. Edit (or add) a `rules/<id>.md` file. New rules must be listed in `rules/_sections.md`.
2. Run the build to refresh the bundles and compiled docs:

   ```bash
   python scripts/build_skills.py
   ```

   It fails loudly if a declared rule has no file, or a rule file isn't declared in
   `_sections.md`. The generated `skills/*/rules/` and `skills/*/AGENTS.md` are committed so the
   skills install self-contained — always re-run the build (and commit the result) after editing
   `rules/`.
