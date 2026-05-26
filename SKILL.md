---
name: prd-generator
description: Conduct structured interviews to generate production-ready PRD documents. Actively use this skill whenever a user wants to build software or expresses a product idea that needs requirements analysis — even vague ones. This includes phrases like "I want to build...", "I have an idea for...", "help me design/plan...", "write a PRD/spec for...", "analyze requirements for...", "梳理需求", "需求文档", "帮我设计一个...". Also triggers when a user wants to analyze an existing codebase ("analyze my project", "帮我看看这个项目", "我这个项目需要...") or revise an existing PRD ("update the PRD", "iterate on the spec", "改一下需求"). The skill auto-detects project type, persists interview progress, and outputs modular documentation an agent can directly code from.
---

# PRD Generator

Generate production-ready software requirement documents through structured, layered interviews. Supports three modes: greenfield (from scratch), retrospective (from existing codebase), and iteration (revising a previous PRD). The output is a complete PRD that an agent can follow to build the software with minimal back-and-forth.

## Mode Selection (Phase 0)

Before anything else, determine which mode the user needs. Read `references/interview-guide.md` for detailed handling of edge cases.

### Step 0.1: Check for existing state

Look for `.interview-state.json` in the current working directory. If found and `status` is not "complete", read `current_phase`, `mode`, and `completed_questions` and resume from where things left off. Summarize what's been covered, then continue with the next unanswered question.

### Step 0.2: Detect the mode

Scan the user's request and the working directory:

```
What is the user saying?
├── "I have an idea / I want to build / 我想做一个..."
│   AND CWD has no *-prd/ directory
│   AND CWD is not a code repository
│   → GREENFIELD mode (from scratch)
│
├── "Analyze my project / 帮我看看这个项目 / 分析一下..."
│   AND CWD looks like a code repository (has package.json, src/, etc.)
│   → RETROSPECTIVE mode (from codebase)
│
├── "Update the PRD / modify the spec / 改一下需求 / 迭代..."
│   AND CWD has a *-prd/ directory or .interview-state.json with status=complete
│   → ITERATION mode (revise existing PRD)
│
└── Ambiguous — none of the above clearly match
    → ASK: "Are you starting a new project from scratch, analyzing an existing codebase,
            or revising a previous PRD?"
```

### Step 0.3: Language detection

Detect the user's language from their opening message. If Chinese, conduct the entire interview and produce all output in Chinese (Simplified, zh-CN). If English, use English. If mixed or ambiguous, ask. Save as `language` in the state file.

### State file structure

```json
{
  "project_name": "",
  "project_type": "",
  "mode": "greenfield | retrospective | iteration",
  "current_phase": "0-bootstrap",
  "status": "in_progress",
  "language": "zh-CN",
  "version": 1,
  "previous_version_dir": "",
  "change_summary": [],
  "phases": {
    "1-vision": {"status": "pending", "answers": {}},
    "2-features": {"status": "pending", "answers": {}},
    "3-technical": {"status": "pending", "answers": {}},
    "4-details": {"status": "pending", "answers": {}}
  },
  "conflicts": [],
  "mvp_notes": [],
  "completed_questions": []
}
```

Save after every Q&A exchange. Do not wait until the end of a phase.

---

## Greenfield Mode (from scratch)

The original flow: user has an idea, no code yet, no existing PRD.

### Phase G1: Vision & Scope

Goal: understand what problem this solves, for whom, and what success looks like.

Ask these questions, one at a time (3-5 per message max):

1. **Project name** — What would you like to call this project? (a working title is fine)
2. **The problem** — What problem does this software solve? Why does someone need this?
3. **Target users** — Who will use this? Describe the primary user personas. Are there secondary users?
4. **Core value proposition** — In one sentence, what's the main thing this product does for its users?
5. **Success criteria** — How will you know this is successful?
6. **Competitors / alternatives** — What do people currently do to solve this problem? What makes this different?

After Phase G1, detect the project type. Read `references/project-types.md` for the detection matrix. Present your assessment: "Based on what you've described, this looks like a **[type]** project (rationale). Does that sound right?"

### Phase G2: Features & Scope

Goal: map out functionality, prioritize, and define the MVP boundary.

Read `references/domain-questions.md` and pick the most relevant domain template. Use these as a starting point but adapt freely.

1. **Core features** — What are the essential things users must be able to do?
2. **Prioritization** — For each feature: P0 (can't launch without), P1 (soon after launch), P2 (nice to have). If the user isn't sure, ask: "If you could only ship ONE thing, what would it be?"
3. **MVP boundary** — Define what's in and explicitly what's NOT in the MVP. Write a clear line: "The MVP includes X. It does NOT include Y."
4. **User journeys** — Walk through the 2-3 most critical user flows step by step. For each: "What does the user do first? Then what? What happens if something goes wrong?"

### Phase G3: Technical & Architecture

Goal: determine the technical shape without over-specifying.

1. **Platform & deployment** — Where will this run? Web SPA or SSR? Mobile native or cross-platform? Hosting?
2. **Data & storage** — What kind of data? Files? Real-time? What database shape?
3. **External integrations** — Payment, email/SMS, third-party login, maps, cloud storage, analytics, existing systems.
4. **Auth & permissions** — Accounts needed? Roles? Compliance (GDPR, PIPL)?
5. **Non-functional requirements** — Performance, concurrency, security, offline, accessibility, i18n.

After gathering answers, check for conflicts. Read `references/conflict-patterns.md`. Surface tensions and ask the user to resolve.

Read `references/architecture-patterns.md` and recommend an architecture pattern. Explain the recommendation in plain language.

### Phase G4: Polish & Edge Cases

Goal: catch what's been missed and handle uncomfortable cases.

1. **Edge cases** — Empty states, error states, first-time experience, data limits, concurrency conflicts.
2. **Migration & onboarding** — Replacing an existing system? How do users/data transition?
3. **Future expansion** — Which P2 features might become important later? Design data model and API to not require rewrites.
4. **Open questions** — "What are you still unsure about? What decisions are you deferring?" Document with owners and deadlines.

---

## Retrospective Mode (from codebase)

User has an existing codebase and wants to generate or complete a PRD for it. Read `references/retrospective-guide.md` for the full scanning strategy and assessment patterns.

### Phase R0: Project Scan

Do NOT ask the user questions yet. First, explore the codebase automatically:

1. Identify the tech stack — read `package.json`, `requirements.txt`, `Cargo.toml`, `go.mod`, etc.
2. Understand project structure — walk the directory tree 2-3 levels deep
3. Read existing docs — `README.md`, `CHANGELOG.md`, `docs/` if present
4. Check git history — `git log --oneline -20` if it's a git repo
5. Spot-check key source files — entry point, a route/controller, a data model, a config file

### Phase R1: Present Assessment

Tell the user what you found:

- **What this project is** — one-sentence summary from code evidence
- **Tech stack** — languages, frameworks, databases
- **Current features** — inferred from routes, components, modules
- **Project health** — tests, CI, documentation quality
- **Notable issues** — security risks, missing error handling, no tests

Ask the user to confirm or correct before proceeding.

### Phase R2: Goal Interview (lightweight)

1. "What was the original goal for this project?" — May differ from what the code now does
2. "What's working well that you want to keep?"
3. "What isn't working? What prompted you to seek a PRD now?"
4. "What's missing that you want to add?"
5. "Should the PRD document everything (existing + planned), or only the gaps and changes?"

### Phase R3: Output

Generate the standard PRD directory structure (see Output Generation section). For each module/feature, annotate its current status:

- **[EXISTS]** — Fully implemented. Describe what exists, include improvement notes.
- **[PARTIAL]** — Partially implemented. Describe what's done and what's missing.
- **[GAP]** — Not yet implemented. Write as you would for greenfield.

---

## Iteration Mode (revise existing PRD)

User has a PRD generated previously and wants to modify it. Read `references/iteration-guide.md` for the full version management and change detection rules.

### Phase I0: Load Old PRD + Backup Warning

1. Find the existing PRD — look for `*-prd/` directories or check `.interview-state.json` for `previous_version_dir`
2. Read ALL files in the old PRD
3. Present a summary: "Your current PRD at `<path>` defines [N] modules with [X] P0 features. The main modules are: [list]. The architecture is [pattern]."
4. **Display backup warning** (before any interview questions):

> Before I generate the new PRD, please manually back up your old PRD if you haven't already. For example:
> `cp -r <project>-prd <project>-prd-backup`
>
> I will generate the new version in a separate directory — your old PRD won't be touched. But it's always safer to have your own backup.

### Phase I1: Change Interview

1. Start open-ended: "What made you want to revise the PRD? What's changed?"
2. Track changes by category: **ADD** (new features), **REMOVE** (dropped features), **MODIFY** (changed features), **REARCHITECT** (structural changes), **SCOPE CHANGE** (audience/NFR shifts)
3. Module-by-module review: for each existing module, ask "Keep as-is? Modify? Drop? Split or merge?"
4. Gap analysis: "What's completely new that wasn't in the old PRD?"

### Phase I2: Cross-check

1. **Dependency check** — If module A is being removed and B depends on it, flag immediately
2. **Conflict check** — Run conflict detection rules against the new combination of requirements
3. **Architecture check** — Does the original architecture still fit? If not, recommend changes

### Phase I3: Output

Generate versioned output (see Output Generation section). Every iteration PRD MUST include a change summary in its README.md. Annotate modules with **[NEW]**, **[MODIFIED]**, **[UNCHANGED]**, or **[REMOVED in vN]**.

After generating, remind again about backup.

---

## Output Generation (all modes)

When the interview is complete, set `status` to "complete" in the state file, then generate.

### Versioned directory naming

- **Greenfield / first retrospective**: `<project-name>-prd/` (no version suffix)
- **First iteration**: `<project-name>-prd-v2/`
- **Subsequent iterations**: auto-increment by scanning existing `<project-name>-prd-v*` directories

### Directory structure

```
<project-name>-prd[-vN]/
├── README.md              # Index + project overview + architecture summary
│                          # (iteration mode: includes change summary)
├── modules/
│   ├── module-01-xxx.md   # P0 features, grouped logically
│   └── module-02-xxx.md   # One file per logical module
├── user-journeys.md       # Core user flows, step by step
├── data-model.md          # Entities, relationships, key fields
├── integrations.md        # External service dependencies
├── acceptance.md          # Per-feature acceptance criteria checklist
└── open-questions.md      # Deferred decisions with owners
```

Use the templates in `assets/templates/` as the starting structure for each file. Read them and fill them in with the collected answers.

### State file after generation

Update `.interview-state.json`:
- `status`: `"complete"`
- `mode`: the mode used
- `version`: the version number (1 for first, 2 for first iteration, etc.)
- `previous_version_dir`: the old PRD directory path (iteration only)

---

## Important Principles

### Why each principle matters

**Don't just collect answers — interrogate them.** Users often say what they think they want, not what they need. When a user says "I need a chat feature," ask why. Maybe they need async communication, and comments or email notifications would serve better. Understanding the underlying need leads to better requirements.

**Surface tensions immediately.** Contradictory requirements ("real-time sync" + "offline mode", "bank-grade security" + "no login required") are normal and expected. Your job is to catch them early, explain the tension, and ask the user to choose. Don't silently accept both.

**Be opinionated about MVP scope.** Most first-time builders try to build too much. After Phase G2 or equivalent, if the P0 list has more than 5-7 major features, push back: "This is ambitious for an MVP. Which of these would you be willing to cut for v1?"

**Use the user's language, not engineering jargon.** A small business owner saying "I want customers to book appointments online" shouldn't get back a document filled with "CRUD operations on Appointment entity with RBAC middleware." Write for all stakeholders.

**Every requirement needs a "why" and a "how to verify."** For each feature in the output, include both the rationale and the acceptance criteria. This is what makes the PRD actionable for an agent.

**Never overwrite the old PRD (iteration mode).** Always generate to a new versioned directory. Always remind the user to back up. Never assume the old version is disposable.

**In retrospective mode, annotate completion status.** Every module must be marked [EXISTS], [PARTIAL], or [GAP] so the user and future agents know what's real vs. planned without re-reading the codebase.

## Conflict Detection Rules

Watch for these common contradictions during any interview:

- Offline + real-time collaboration (offline edits cause merge conflicts)
- Maximum security + frictionless UX (every auth check adds friction)
- Unlimited scale + fixed budget (scale costs money)
- "Simple like Twitter" + feature lists of 20+ items
- GDPR/privacy compliance + "we'll figure out data handling later"
- Multi-tenant + each client wants custom schema

When detected, explain the tradeoff in plain language and ask the user which side matters more.

In iteration mode, also check: new additions conflicting with existing architecture, removed modules breaking dependencies, scope changes invalidating previous NFR decisions.

## Reference Files

- `references/interview-guide.md` — Interview techniques and difficult scenarios (all modes)
- `references/project-types.md` — Project type detection matrix and type-specific questions
- `references/domain-questions.md` — Domain-specific question templates
- `references/architecture-patterns.md` — Architecture patterns with plain-language explanations
- `references/conflict-patterns.md` — Conflict detection patterns with resolution strategies
- `references/retrospective-guide.md` — Project scanning strategy, code signals, progress assessment
- `references/iteration-guide.md` — Version management, change classification, dependency checking

## Asset Templates

- `assets/templates/README-template.md`
- `assets/templates/module-template.md`
- `assets/templates/journeys-template.md`
- `assets/templates/data-model-template.md`
- `assets/templates/integrations-template.md`
- `assets/templates/acceptance-template.md`

Read each template when generating the corresponding output file.
