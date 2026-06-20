# CollectiveAI Skills

Agent skills encoding our engineering standards. The rules are derived from real production
practice across our Python, FastAPI, Prefect, LLM/AI, ASR, and Next.js codebases, plus Matt
Pocock's deep-module skills — not from generic defaults. They are written to be self-contained
and usable by anybody.

## Skills

- `collective-refactor` — refactor an existing codebase toward CollectiveAI style and architecture.
- `collective-pr-review` — PR gate before creating, opening, updating, or marking a PR ready.

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
- `applies-to` — `all`, or a comma list of repos/stacks (`nextjs`, `prefect`, `qxo`, …). This is
  how the pack avoids imposing one repo's config on every repo.
- `status` — `current` (what repos do today), `direction` (target for new code), or `legacy`
  (preserve, don't expand).
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
   skills install self-contained — always re-run the build after editing `rules/`.

## Install

The included plugin manifests (`.claude-plugin/plugin.json`, `.codex-plugin/plugin.json`) expose
both skills. For direct local use, copy the skill directories into the agent skills directory —
each one already carries its own `rules/` and `AGENTS.md`.
