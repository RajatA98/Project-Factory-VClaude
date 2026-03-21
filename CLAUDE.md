# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

The full Project Factory rules and workflow are defined in `.claude/CLAUDE.md`. Claude Code automatically reads that file. This root file serves as a pointer for anyone browsing the repo.

## What This Repo Is

Project Factory is a **skills pipeline repo** for Claude Code. It contains command files and artifact templates that guide a user through turning a raw idea into a shipped product.

## Quick Reference

- **Start here:** `/start-project` (guided workflow for new projects)
- **Global rules:** `.claude/CLAUDE.md`
- **Commands:** `.claude/commands/` (invoke with `/command-name`)
- **Artifacts:** `factory/artifacts/` (pipeline state and outputs)

## Getting Started

Run `/start-project` to begin. It guides you through the entire pipeline automatically with explanations and approval at every step.

Individual phase commands (`/understand`, `/prd`, `/presearch`, `/decide`, `/plan`, `/implement`, `/review`, `/test-qa`, `/ship`) are available for re-running specific phases or advanced use. `/teach`, `/teach-implement`, and `/interview` can be used at any time for learning.

## Specialized Commands

- `/arch-docs` — Generate architecture documentation, decision logs, and privacy analysis
- `/golden-evals-architect` — Create golden evaluation test suites for AI agents
- `/labeled-scenarios` — Generate labeled scenario coverage maps
- `/replay-harness` — Design replay harnesses and Stage 3 evaluation frameworks
- `/rubric-evals` — Build scoring rubrics and Stage 4 quality evaluation
