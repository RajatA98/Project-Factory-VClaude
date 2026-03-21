# Project Factory

A Claude Code skills pipeline that guides you from a raw idea to a shipped product.

## What Is This?

Project Factory is a set of Claude Code commands and artifact templates that walk you through a structured 9-phase pipeline. It is designed for people who have **no software engineering experience** — Claude teaches you as you go using direct instruction methodology.

This is not a software product. It is a **workflow repo** meant to be used directly inside Claude Code.

## How to Use

1. Open this repo in Claude Code
2. Run `/start-project` and describe your idea
3. Claude guides you through each phase automatically — explaining, teaching, and asking for approval at every step
4. Use `/teach` at any time to get explanations

> `/start-project` is the recommended entry point. It walks you through the full pipeline. Individual phase commands are available for re-running specific phases or advanced use.

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
| `/teach-implement` | Explain code and implementation details |

## Recommended Flow

Interleave learning with each phase:

```
/understand → /teach → /prd → /teach → /presearch → /teach → /decide → /teach
→ /plan → /teach → /implement → /teach-implement → /review → /teach
→ /test-qa → /teach → /ship → /teach
```

## Artifacts

All pipeline outputs are stored in `factory/artifacts/`. These files are the source of truth for the workflow — each phase reads from previous artifacts and writes its own.

## Structure

```
.claude/
  CLAUDE.md              # Global rules for Claude
  commands/              # Pipeline command files (11 commands)
factory/
  artifacts/             # Pipeline state and outputs (12 artifacts)
```
