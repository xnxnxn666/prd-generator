# PRD Generator

A Claude Code skill that conducts structured interviews to transform vague product ideas into production-ready PRD documents. The output is modular, agent-actionable, and covers everything from user journeys to acceptance criteria.

## Quick Start

```bash
npx skills add https://github.com/<your-username>/prd-generator -g
```

Then just describe your idea — the skill triggers automatically:

> "我想做一个宠物寄养平台，用户可以发布寄养需求..."

> "I want to build a CRM for small businesses, but I don't know where to start..."

## What It Does

- **5-phase layered interview** — Vision → Features → Architecture → Edge Cases → Output
- **Auto-detects project type** — Web, Mobile, Desktop, CLI, API
- **Progress persistence** — Every Q&A saved to `.interview-state.json`, resume anytime
- **Conflict detection** — Flags contradictory requirements before they reach development
- **MVP boundary enforcement** — Pushes back on scope creep, keeps v1 manageable
- **Bilingual** — Auto-detects Chinese/English from your first message

## Output

```
<project>-prd/
├── README.md           # Index + architecture summary
├── modules/
│   ├── module-01-*.md  # Per-module feature specs with acceptance criteria
│   └── module-02-*.md
├── user-journeys.md    # Step-by-step flows with error handling
├── data-model.md       # Entity relationships and constraints
├── integrations.md     # External service dependencies
├── acceptance.md       # Verifiable checklist per feature
└── open-questions.md   # Deferred decisions with owners
```

Each module file is self-contained — an agent can load them one at a time while building, without consuming unnecessary context.

## Install from `.skill` file

Download `prd-generator.skill` from [Releases](https://github.com/<your-username>/prd-generator/releases), then:

```bash
npx skills add ./prd-generator.skill -g
```

## Development

Built with [skill-creator](https://github.com/anthropics/skills) using the TDD workflow:

- **Draft** → **Eval** (3 test cases × 2 configurations) → **Grade** → **Benchmark** → **Repeat**

| Configuration | Pass Rate |
|---|---|
| With Skill | 100% (24/24 assertions) |
| Baseline (no skill) | 83.3% (20/24 assertions) |

Baseline failures were all structural (missing modular directory layout, missing project type identification, missing architecture rationale) — exactly the gaps this skill is designed to fill.

## License

MIT
