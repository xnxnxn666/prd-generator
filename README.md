# PRD Generator

A Claude Code skill that transforms product ideas (or existing codebases) into production-ready PRD documents. The output is modular, agent-actionable, and covers everything from user journeys to acceptance criteria.

## Quick Start

```bash
npx skills add https://github.com/xnxnxn666/prd-generator -g
```

Then just describe your need — the skill triggers automatically:

> "我想做一个宠物寄养平台，用户可以发布寄养需求..."

> "I want to build a CRM for small businesses, but I don't know where to start..."

> "帮我分析一下这个项目，代码写了一半了，需要规范和PRD"

> "之前的 PRD 要改几个需求，加一个统计面板，去掉私信功能"

## Three Modes

### Greenfield (from scratch)

You have an idea but no code. The skill conducts a 5-phase interview (Vision → Features → Architecture → Edge Cases → Output) to turn your idea into a complete PRD.

### Retrospective (from codebase)

You've already written code — half-finished, fully built, or inherited from someone else. The skill scans your codebase, reports what it finds, then interviews you about gaps and direction. The output PRD annotates every module with its implementation status: **[EXISTS]**, **[PARTIAL]**, or **[GAP]**.

### Iteration (revise an existing PRD)

You used this skill before and now need to change requirements. The skill reads your old PRD, interviews you about what's changing (add/remove/modify), cross-checks dependencies and conflicts, then generates a **new versioned PRD** (`-v2/`, `-v3/`, ...). Your old PRD is never touched. You're reminded to back it up at the start and end of the process.

## What It Does

- **Three-mode detection** — Greenfield, retrospective, or iteration. Auto-detected from your request and working directory.
- **5-phase layered interview** — Vision → Features → Architecture → Edge Cases → Output
- **Auto-detects project type** — Web, Mobile, Desktop, CLI, API
- **Progress persistence** — Every Q&A saved to `.interview-state.json`, resume anytime
- **Conflict detection** — Flags contradictory requirements before they reach development
- **MVP boundary enforcement** — Pushes back on scope creep, keeps v1 manageable
- **Versioned output** — Iteration mode generates `-v2/`, `-v3/` without overwriting old PRDs
- **Bilingual** — Auto-detects Chinese/English from your first message

## Output

```
<project>-prd[-vN]/
├── README.md           # Index + architecture summary (+ change summary for iterations)
├── modules/
│   ├── module-01-*.md  # Per-module feature specs with acceptance criteria
│   └── module-02-*.md
├── user-journeys.md    # Step-by-step flows with error handling
├── data-model.md       # Entity relationships and constraints
├── integrations.md     # External service dependencies
├── acceptance.md       # Verifiable checklist per feature
└── open-questions.md   # Deferred decisions with owners
```

Each module file is self-contained — an agent can load them one at a time while building.

## Install from `.skill` file

Download `prd-generator.skill` from [Releases](https://github.com/xnxnxn666/prd-generator/releases), then:

```bash
npx skills add ./prd-generator.skill -g
```

## Development

Built with [skill-creator](https://github.com/anthropics/skills) using the TDD workflow:

- **Draft** → **Eval** → **Grade** → **Benchmark** → **Repeat**

| Configuration | Pass Rate |
|---|---|
| With Skill | — |
| Baseline (no skill) | — |

## License

MIT
