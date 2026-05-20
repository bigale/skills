# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

A collection of **Claude Code skills** — not application code. Each skill is a single `SKILL.md` file (YAML frontmatter + markdown prompt) that teaches Claude Code a specific workflow. There is no build step, no dependencies, no tests. Editing a skill means editing its prompt.

The skills encode a Domain-Driven Design workflow for AI-assisted development. They are intentionally **project-agnostic**: they discover context at runtime by reading the consuming project's `docs/CONTEXT_MAP.md`, `docs/UBIQUITOUS_LANGUAGE.md`, `docs/contexts/*/CONTEXT.md`, and `CLAUDE.md`. Do not bake tech-stack or folder assumptions into a skill.

## Layout

One directory per skill, each containing exactly one `SKILL.md`:

- `spec/` — interview → PRD grounded in the domain model
- `domain/` — stress-test a plan against the domain model; updates docs inline
- `slice/` — break a spec into vertical tracer-bullet slices
- `tdd/` — pragmatic test-driven implementation of a slice
- `holistic/` — zoom out to map bounded contexts and blast radius
- `architect/` — surface friction; design deep module interfaces with parallel sub-agents
- `qa/` — conversational bug reporting → durable GitHub issues (note: `name:` in frontmatter is `ddd-qa` to avoid colliding with `gstack`'s `/qa`)

The intended flow is `spec → domain → slice → tdd`, with `holistic`, `architect`, and `qa` as supporting skills. Cross-references between skills use the `Related skills` section at the bottom of each `SKILL.md`.

## Install / activate during development

```bash
./install.sh
```

Symlinks every top-level directory in this repo into `~/.claude/skills/<name>/`. Because it's a symlink, edits to a `SKILL.md` here take effect immediately in any project — no re-install needed after the first run.

## SKILL.md conventions

Every skill file follows this shape:

```yaml
---
name: <slug>                   # the slash-command name; lowercase, kebab-case
description: <one paragraph>   # shown in the skill picker; lead with what it does, end with "Use when ..."
user-invocable: true
argument-hint: "[what to pass]"
---
```

Notes when editing or adding skills:

- `name:` is what becomes `/<name>` — it does not have to match the directory name (see `qa/` → `ddd-qa`), but matching is preferred unless there's a collision with another installed skill set.
- The body is a prompt, not documentation. Write it as instructions to the future Claude session that will run the skill (`Read X.`, `Ask the user Y.`, `Write to Z.`), not as prose explaining the skill to a human reader.
- Keep skills project-agnostic. If a skill needs project-specific knowledge, it should read it from `docs/...` at runtime, not hard-code it.
- Domain-doc discovery order is consistent across skills: `docs/CONTEXT_MAP.md` → `docs/UBIQUITOUS_LANGUAGE.md` → `docs/contexts/*/CONTEXT.md` → `CLAUDE.md`. New skills should follow the same order so users get predictable behavior.
- Lazy file creation: if a doc file doesn't exist, the skill should create it only when there's something concrete to write — never up front.

## Principles baked into the skills (preserve when editing)

- **Vertical slices, never horizontal.** Each slice cuts schema → backend → API → UI → tests. Slices that are "set up the database" or "wire the API" are anti-patterns.
- **Pragmatic TDD.** Test-first for core logic and context boundaries; implementation-first for UI and glue. Mock only at true system boundaries (external APIs, time, randomness). Never mock your own modules.
- **Deep modules.** Small interfaces hiding significant complexity (Ousterhout). The `/architect` skill explicitly designs for this.
- **Domain language everywhere.** Specs, tests, issues, ADRs all use terms from `UBIQUITOUS_LANGUAGE.md`. Issues and ADRs avoid file paths and line numbers because those go stale.
- **ADRs are rare.** Only when all three hold: hard to reverse, surprising without context, real trade-off. Keep them to title + 1–3 sentences.
