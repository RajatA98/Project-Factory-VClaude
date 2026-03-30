# Project Factory

A Claude Code skills pipeline that guides you from a raw idea to a shipped product.

## What Is This?

Project Factory is a set of Claude Code commands and artifact templates that walk you through a structured 9-phase pipeline. It is designed for people who have **no software engineering experience** — Claude teaches you as you go using direct instruction methodology.

This is not a software product. It is a **workflow repo** meant to be used directly inside Claude Code.

## How to Use

### New Projects

1. Open this repo in Claude Code
2. Run `/start-project` and describe your idea
3. Claude guides you through each phase automatically — explaining, teaching, and asking for approval at every step

> `/start-project` is the recommended entry point. It walks you through the full pipeline. Individual phase commands are available for re-running specific phases or advanced use.

### Existing Codebases

1. Open your project in Claude Code (with this repo's commands available)
2. Run `/enhance-project`
3. Claude audits your codebase, compares it against requirements, discusses priorities, and creates a gap-driven plan

## Pipeline Phases

| # | Command | Purpose |
|---|---------|---------|
| 1 | `/understand` | Interview to clarify your idea |
| 2 | `/prd` | Write product requirements |
| 3 | `/presearch` | Research technology options |
| 4 | `/decide` | Lock in technology and product decisions |
| 5 | `/plan` | Break work into implementation phases |
| 6 | `/implement` | Build it using TDD, one slice at a time |
| 7 | `/review` | Review implementation quality |
| 8 | `/test-qa` | Evaluate test coverage and quality |
| 9 | `/ship` | Prepare environment, deployment, and release |

### Learning Commands

| Command | Purpose |
|---------|---------|
| `/teach` | Explain the current phase to a complete beginner |
| `/teach-implement` | Explain code and implementation details (interview-level depth) |
| `/interview` | Mock interview with 3 questions based on what was built, with feedback |

### Enhancement Commands

| Command | Purpose |
|---------|---------|
| `/audit` | Analyze an existing codebase (technology, features, quality) |
| `/gaps` | Compare PRD requirements against audited codebase |
| `/polish-ui` | Review frontend for AI-generated patterns and create an authenticity improvement plan |

### Specialized Commands

| Command | Purpose |
|---------|---------|
| `/arch-docs` | Generate architecture documentation, decision logs, and privacy analysis |
| `/golden-evals-architect` | Create golden evaluation test suites for AI agents |
| `/labeled-scenarios` | Generate labeled scenario coverage maps |
| `/replay-harness` | Design replay harnesses and Stage 3 evaluation frameworks |
| `/rubric-evals` | Build scoring rubrics and Stage 4 quality evaluation |

## Recommended Flow

### New Projects

```
/start-project → (Understand, PRD, Presearch, Decide, Plan)
  → /implement → /teach-implement → /interview
  → /review → /teach → /test-qa → /teach → /ship → /teach
```

### Existing Codebases

```
/enhance-project → (Audit, Gaps, Discussion, Plan)
  → /implement → /teach-implement → /interview
  → /review → /teach → /test-qa → /teach → /ship → /teach
```

## Artifacts

All pipeline outputs are stored in `factory/artifacts/`. These files are the source of truth for the workflow — each phase reads from previous artifacts and writes its own.

| Artifact | Written By |
|----------|-----------|
| `PROBLEM_SUMMARY.md` | /understand |
| `PRD.md` | /prd |
| `PRESEARCH.md` | /presearch |
| `LOCKED_DECISIONS.md` | /decide |
| `PROJECT_PLAN.md` | /plan |
| `IMPLEMENTATION_LOG.md` | /implement |
| `REVIEW_REPORT.md` | /review |
| `TEST_REPORT.md` | /test-qa |
| `ENV_SETUP.md` | /ship |
| `DEPLOYMENT.md` | /ship |
| `RELEASE.md` | /ship |
| `CODEBASE_AUDIT.md` | /audit, /enhance-project |
| `GAP_ANALYSIS.md` | /gaps, /enhance-project |
| `UI_POLISH_REPORT.md` | /polish-ui, /enhance-project |
| `LEARNING_NOTES.md` | /teach, /teach-implement |

## Structure

```
.claude/
  CLAUDE.md              # Global rules for Claude
  commands/              # Pipeline command files (22 commands)
factory/
  artifacts/             # Pipeline state and outputs (15 artifacts)
```
