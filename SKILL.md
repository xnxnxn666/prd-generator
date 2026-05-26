---
name: prd-generator
description: Conduct structured interviews to generate production-ready PRD documents. Actively use this skill whenever a user wants to build software or expresses a product idea that needs requirements analysis — even vague ones. This includes phrases like "I want to build...", "I have an idea for...", "help me design/plan...", "write a PRD/spec for...", "analyze requirements for...", "梳理需求", "需求文档", "帮我设计一个...". Do NOT wait for an explicit "PRD" request — any scenario where a user describes a software idea that lacks concrete requirements should trigger this skill. The skill auto-detects project type, persists interview progress, and outputs modular documentation an agent can directly code from.
---

# PRD Generator

Generate production-ready software requirement documents through structured, layered interviews. The output is a complete PRD that an agent can follow to build the software with minimal back-and-forth.

## Overview

This skill transforms vague product ideas into concrete, actionable PRDs through a 5-phase interview process. Each phase builds on the previous one. Interview progress is saved after every Q&A pair — if the conversation is interrupted, resume from the last saved state.

Read `references/interview-guide.md` for detailed guidance on handling common interview edge cases and difficult scenarios.

## The 5-Phase Interview Process

### Phase 0: Bootstrap

Before asking any questions, check for an existing interview state file:

- Look for `.interview-state.json` in the current working directory
- If found and `status` is not "complete", read the `current_phase` and `completed_questions` and resume from where things left off. Summarize what's been covered so far for the user, then continue with the next unanswered question.
- If not found, create a new one and start fresh

**Language detection.** Before the first question, detect the user's language from their opening message. If the user writes in Chinese, conduct the entire interview and produce all output in Chinese (Simplified, zh-CN). If English, use English. If mixed or another language, ask: "Which language should I use for this PRD?" The SKILL.md body and references are in English (internal instructions), but all user-facing communication and generated output must match the detected language. Save the language choice to the state file as `language`.

The state file structure:
```json
{
  "project_name": "",
  "project_type": "",
  "current_phase": "0-bootstrap",
  "status": "in_progress",
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

After each Q&A exchange, immediately write the updated state to `.interview-state.json`. Do not wait until the end of a phase — save after every single answer.

### Phase 1: Vision & Scope

Goal: understand what problem this solves, for whom, and what success looks like.

Ask these questions, one at a time (3-5 per message max). Save progress after each answer.

1. **Project name** — What would you like to call this project? (a working title is fine)
2. **The problem** — What problem does this software solve? Why does someone need this?
3. **Target users** — Who will use this? Describe the primary user personas. Are there secondary users (admins, moderators, etc.)?
4. **Core value proposition** — In one sentence, what's the main thing this product does for its users?
5. **Success criteria** — How will you know this is successful? (e.g., "a user can complete X in under Y minutes", "supports Z concurrent users")
6. **Competitors / alternatives** — What do people currently do to solve this problem? Are there existing products? What makes this different?

After Phase 1 answers are complete, detect the project type. Read `references/project-types.md` for the type detection matrix. Present your assessment to the user: "Based on what you've described, this looks like a **[type]** project (rationale). Does that sound right?" Save the confirmed type to the state file.

### Phase 2: Features & Scope

Goal: map out functionality, prioritize, and define the MVP boundary.

First, read `references/domain-questions.md` and pick the most relevant domain template based on the project type and description from Phase 1. Use the domain-specific questions as a starting point, but adapt freely.

Ask these questions, layered with domain-specific ones:

1. **Core features** — What are the essential things users must be able to do? List everything that comes to mind. We'll prioritize next.
2. **Prioritization** — For each feature mentioned, assign a priority: **P0** (can't launch without it), **P1** (needed soon after launch), **P2** (nice to have). If the user isn't sure, help them decide by asking: "If you could only ship ONE thing, what would it be?"
3. **MVP boundary** — Based on P0 features, define what the minimum viable product looks like. Explicitly state what is NOT in the MVP. This is critical — write a clear line: "The MVP includes X. It does NOT include Y."
4. **User journeys** — Walk through the 2-3 most critical user flows step by step. For each: "What does the user do first? Then what? What happens if something goes wrong?"

### Phase 3: Technical & Architecture

Goal: determine the technical shape of the project without over-specifying.

Ask these questions, adapted to the project type:

1. **Platform & deployment** — Where will this run? (Web browser, mobile app, desktop, server, embedded?) For web: SPA or SSR? For mobile: native or cross-platform? Who handles hosting/deployment?
2. **Data & storage** — What kind of data does this system manage? Is there user-generated content? Files? Real-time data? Does it need a database, and if so, what shape of data?
3. **External integrations** — Does this need to connect to anything outside itself? Consider: payment processing, email/SMS, third-party login (Google, Apple, WeChat), maps, cloud storage, analytics, existing internal systems.
4. **Auth & permissions** — Do users need accounts? What roles exist? Is there content that some users can see but others can't? Are there compliance requirements (GDPR, HIPAA, China's PIPL)?
5. **Non-functional requirements** — Ask about: performance expectations, concurrency, security sensitivity, offline capability, accessibility, i18n/l10n needs.

After gathering answers, check for conflicts between requirements (e.g., "offline-first" + "real-time collaboration" are in tension). If conflicts exist, surface them and ask the user to resolve. Save conflicts to the state file.

Read `references/architecture-patterns.md` and recommend an architecture pattern that fits the project type and requirements. Explain the recommendation — don't assume the user knows the terminology.

### Phase 4: Polish & Edge Cases

Goal: catch what's been missed and handle the uncomfortable cases.

1. **Edge cases inventory** — Prompt the user to think about: empty states (what does the user see when there's no data?), error states (what happens when the network fails mid-operation?), first-time experience, data limits, concurrent usage conflicts.
2. **Migration & onboarding** — If this replaces an existing system or process, how do existing users/data transition? Is there an import step?
3. **Future expansion** — What features were marked P2 that might become important later? Design the data model and API with these in mind so they don't require a rewrite.
4. **Open questions** — Ask: "What are you still unsure about? What decisions are you deferring?" Document these explicitly with owners and deadlines.

### Phase 5: Output Generation

When all phases are complete, set `status` to "complete" in the state file. Then generate the output.

Generate this directory structure:
```
<project-name>-prd/
├── README.md              # Index + project overview + architecture summary
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

## Important Principles

### Why each principle matters

**Don't just collect answers — interrogate them.** Users often say what they think they want, not what they need. When a user says "I need a chat feature," ask why. Maybe they need async communication, and comments or email notifications would serve better. Understanding the underlying need leads to better requirements.

**Surface tensions immediately.** Contradictory requirements ("real-time sync" + "offline mode", "bank-grade security" + "no login required") are normal and expected. Your job is to catch them early, explain the tension, and ask the user to choose. Don't silently accept both — that produces a PRD no one can build.

**Be opinionated about MVP scope.** Most first-time builders try to build too much. After Phase 2, if the P0 list has more than 5-7 major features, push back: "This is ambitious for an MVP. Which of these would you be willing to cut for v1?" The skill should be the voice of restraint, not just a scribe.

**Use the user's language, not engineering jargon.** A small business owner saying "I want customers to book appointments online" shouldn't get back a document filled with "CRUD operations on Appointment entity with RBAC middleware." Write for all stakeholders — the business person, the designer, AND the developer — all in the same document.

**Every requirement needs a "why" and a "how to verify."** For each feature in the output, include both the rationale (why this matters to the user/business) and the acceptance criteria (how you'd know it's done correctly). This is what makes the PRD actionable for an agent.

## Conflict Detection Rules

Watch for these common contradictions during the interview:

- Offline + real-time collaboration (offline edits cause merge conflicts)
- Maximum security + frictionless UX (every auth check adds friction)
- Unlimited scale + fixed budget (scale costs money)
- "Simple like Twitter" + feature lists of 20+ items
- GDPR/privacy compliance + "we'll figure out data handling later"
- Multi-tenant + each client wants custom schema

When detected, don't just flag it — explain the tradeoff in plain language and ask the user which side matters more.

## Reference Files

- `references/interview-guide.md` — Detailed interview techniques and common difficult scenarios
- `references/project-types.md` — Project type detection matrix and type-specific question addendums
- `references/domain-questions.md` — Domain-specific question templates (ecommerce, SaaS, social, admin panel, etc.)
- `references/architecture-patterns.md` — Common architecture patterns with plain-language explanations
- `references/conflict-patterns.md` — Extended conflict detection patterns with resolution strategies
- `references/output-example.md` — A complete example PRD for reference

## Asset Templates

- `assets/templates/README-template.md`
- `assets/templates/module-template.md`
- `assets/templates/journeys-template.md`
- `assets/templates/data-model-template.md`
- `assets/templates/integrations-template.md`
- `assets/templates/acceptance-template.md`

Read each template when generating the corresponding output file. They contain the structure — fill them with the specific answers collected during the interview.
