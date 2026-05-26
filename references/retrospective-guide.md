# Retrospective Analysis Guide

When a user wants to generate a PRD from an existing codebase (not from scratch), use this guide. The process reverses the normal flow: instead of asking what they want to build, you first observe what's already been built, then interview about gaps and direction.

## Scanning Strategy

### Step 1: Identify the tech stack

Look for these files in order of likelihood:

- `package.json` → Node/JS/TS ecosystem. Check `dependencies` for frameworks (React, Next.js, Express, etc.)
- `requirements.txt` / `pyproject.toml` / `setup.py` → Python ecosystem
- `Cargo.toml` → Rust
- `go.mod` → Go
- `composer.json` → PHP
- `Gemfile` → Ruby
- `pom.xml` / `build.gradle` → Java/Kotlin
- `CMakeLists.txt` → C/C++
- `.csproj` / `.sln` → .NET

If none found, look at file extensions in the top-level directory to guess.

### Step 2: Understand project structure

Walk the directory tree to depth 2-3. Note patterns that reveal the project type:

- `src/`, `components/`, `pages/` → frontend project
- `routes/`, `controllers/`, `models/` → MVC backend
- `api/`, `services/`, `db/` → API/server project
- `app/`, `templates/`, `static/` → full-stack framework (Django, Rails, Laravel)
- `tests/` or `__tests__/` scattered → testing culture present or absent
- `docker-compose.yml`, `Dockerfile` → containerized
- `.github/workflows/` → CI/CD exists

### Step 3: Read existing documentation

- `README.md` — the author's own description. Note what they emphasize vs. omit.
- `CHANGELOG.md` — history of features and fixes
- `docs/` or `documentation/` directory if present

### Step 4: Check git history

Run `git log --oneline -20` (if git repo). Look for:

- Commit message themes — bug fixes dominating = stability issues; feature commits = active growth
- Recent activity — actively maintained or stalled?
- Branch structure — multiple branches = parallel work streams

### Step 5: Spot-check source files

Pick 2-4 key files based on the project structure:

- The main entry point (`index.js`, `main.py`, `app.ts`, etc.)
- A representative route/controller
- A data model / schema file
- A config file

Look for: code organization quality, error handling patterns, hardcoded values, comments/documentation quality, test coverage.

## Signals → Assessment

| Signal | What it means |
|---|---|
| Well-structured directories, consistent naming | Deliberate architecture |
| No tests directory | Untested — mention as risk |
| Comprehensive README with setup instructions | Project is shared/intended for others |
| Many TODO/FIXME comments | Known gaps exist |
| Hardcoded credentials or IPs | Security risk — flag it |
| ORM migrations present | Database schema is version-controlled |
| `.env.example` exists | Configuration is intentional |
| Large files (>500 lines) without clear sections | May be poorly structured |
| Multiple package managers (package.json + yarn.lock + pnpm-lock) | Inconsistent tooling |

## Presenting the Assessment

After scanning, present your findings in this order:

1. **What is this project?** — One-sentence summary from code evidence
2. **Tech stack** — What languages, frameworks, databases you found
3. **Current features** — What the code can already do (inferred from routes, components, modules)
4. **Project health** — Brief: tests present/absent, CI status, documentation quality
5. **Notable issues** — Only flag things that matter: security risks, missing error handling, no tests, inconsistent patterns

Then ask the user to confirm or correct your assessment before moving to the interview phase.

## Retrospective Interview

After the assessment is confirmed, ask:

1. "What was the original goal for this project?" — May differ from what the code does now
2. "What's working well that you want to keep?"
3. "What isn't working? What prompted you to seek a PRD now?"
4. "What's missing that you want to add?"
5. "Do you want the PRD to document everything (existing + planned), or only the gaps and changes?"

## Output Annotations

In the generated PRD, annotate each module/feature with its current status:

- **[EXISTS]** — Fully implemented in the codebase. Describe what exists, include any improvement notes from the user.
- **[PARTIAL]** — Partially implemented. Describe what's done and what's missing.
- **[GAP]** — Not yet implemented. Write as you would for a greenfield PRD.

These annotations help the user (and any future agent) understand what's real vs. planned without re-reading the codebase.
