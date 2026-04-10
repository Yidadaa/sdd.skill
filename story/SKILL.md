---
name: story
description: "Story-driven development workflow for docs/prd and docs/story. Use for story create/update/start/review with a short /story command."
argument-hint: "<create|update|start|review> request=<what to change/build> [prd=<path>] [story=<id>]"
---

# Story Development Skill

## Purpose

Run a reusable story-driven workflow with four commands:

- `create`: generate story planning and story files from request or PRD
- `update`: analyze request, then update `docs/story` and/or versioned `docs/prd`
- `start`: execute development for a target story until that story is complete
- `review`: review staged git changes for code quality, type safety, and style issues

## When To Use

- User asks to revise requirements, story scope, or acceptance criteria
- User asks to split a PRD into stories
- User asks to start implementing one story end-to-end
- User references `docs/prd/*` and `docs/story/*` as source of truth

## Global Rules

1. Read `docs/guideline/overview.md` before feature implementation work.
2. Always inspect existing files under `docs/story/` and `docs/prd/` before editing.
3. If user intent is ambiguous, ask targeted clarification questions first and do not edit files yet.
4. Keep each generated story file under 500 lines.
5. Prefer ASCII text unless existing file clearly uses non-ASCII.
6. Follow existing story naming convention in this repo: `m.n-plan.md`, `m.n-wip.md`, `m.n-done.md`.

## Command: `create`

Use this when user asks to generate stories from a request or a PRD.

### Steps

1. Identify source:

- Explicit PRD path provided by user, or
- Current request as draft requirements.

2. If requirements are incomplete, ask clarifying questions before file creation.
3. Determine next story index `m` by inspecting `docs/story/`.
4. Create planning file `m.0-plan.md` with:

- Scope summary
- Dependency ordering
- Milestones / sub-story breakdown

5. Create sub-story files for executable units using existing status naming (for example `m.1-plan.md`, `m.2-plan.md`, ...).
6. For each story file, include:

- Main goal
- Acceptance criteria checklist
- Functional points
- Unit test case design
- Behavior test case design

7. Ensure each file stays under 500 lines.
8. Report generated files and recommended implementation order.

### Completion Checks

- Planning file exists for the new story group.
- Stories are independently implementable.
- All stories are <= 500 lines.

## Command: `update`

Use this when the user requests requirement/story changes.

### Steps

1. Parse request into one of: PRD update, story update, or both.
2. Read relevant `docs/story/*.md` and `docs/prd/*.md` files to understand current state.
3. If any scope, priority, acceptance criteria, or dependency is unclear, ask clarifying questions.
4. Apply updates:

- Story updates: edit target story files directly.
- PRD updates: do not overwrite old PRD; create a new version file using topic-based increment.

5. When creating PRD version:

- Detect the target PRD topic from user input or selected source file.
- Find highest existing version for that topic in `docs/prd/`.
- Create next version file as `<topic>-v[x+1].md` from the latest effective baseline of that topic.
- Include a detailed "Version Notes" section with what changed and why.
- Remove old prd version from any references in story files if the new version supersedes it.

6. Validate consistency between updated PRD and story files.
7. Report exactly which files changed and what decisions were made.

### Completion Checks

- PRD changes are in a new versioned file, never destructive overwrite.
- Story changes reflect clarified user intent.
- Any unresolved ambiguity is explicitly listed.

## Command: `start`

Use this when user asks to begin implementation for a specific story.

### Steps

1. Confirm target story ID/path from user request.
2. Read target story and dependencies from `docs/story/`.
3. If target or acceptance scope is unclear, ask clarification before coding.
4. Automatically move story status from `plan` to `wip` by renaming the file with the same story id.
5. Implement end-to-end until acceptance criteria are met:

- Code changes
- Tests
- Documentation updates if needed

6. Run validations (tests/lint or focused checks relevant to changed areas).
7. Resolve issues until target story is complete or blocked by an external decision.
8. Automatically move story status from `wip` to `done` by renaming the file with the same story id.
9. Mark acceptance checklist items as complete in the story file when delivered.

### Completion Checks

- All acceptance criteria are completed end-to-end: implementation + tests + story checklist updates.
- Relevant tests pass, or failures/blockers are clearly reported.
- Story status is auto-transitioned consistently (`plan` -> `wip` -> `done`).

## Command: `review`

Use this when the user asks to review staged git diff or recently changed code for quality issues.

### Steps

1. Run `git diff --cached` (staged changes) to get the diff. If nothing is staged, fall back to `git diff HEAD` (unstaged working tree changes).
2. Analyze every changed file for the following categories of issues:

#### Type Safety

- **Avoid overly broad or untyped constructs** — prefer specific types/interfaces over generic escape hatches (e.g. `any`, `object`, `void*`, raw `dict`).
- **Prefer type inference and derivation** — reuse existing type definitions instead of redeclaring equivalent structures.
- **No suppressed type warnings** — fix the root cause instead of silencing the type checker.
- **No duplicated type declarations** — if the same shape is defined in more than one place, consolidate.

#### Redundant / Duplicate Logic

- **Extract repeated code blocks** — if the same logic appears 2+ times across the diff, suggest a shared helper or utility.
- **No copy-pasted conditional branches** — consolidate with a loop, map, or lookup table.
- **No duplicate constants** — reuse from a single source (`constant/` or a top-level `const`).

#### Magic Numbers & Strings

- **No hard-coded magic numbers** — extract to a named constant with a descriptive name.
- **No hard-coded string literals used as identifiers** — use constants or enums from a single source of truth.

#### General Code Style

- **No empty catch blocks** — handle or log the error.
- **No debug logging in production code** — use proper logging or remove before commit.
- **Follow existing project conventions** — match the patterns already established in the codebase for imports, naming, and file organization.

3. For each issue found, output:
   - File path + line number (from the diff context)
   - Issue category
   - Concise description of the problem
   - Suggested fix (code snippet when helpful)

4. Group findings by file, then by category.
5. At the end, provide a brief summary: total issues found per category, and overall risk level (low / medium / high).
6. If no issues are found, confirm the diff looks clean.

### Completion Checks

- All changed files in the diff are covered.
- Each issue has a concrete suggestion, not just a diagnosis.
- Summary is present.

## Clarification Question Templates

Use these when request is not fully clear.

- Scope: "Should this update apply to PRD, stories, or both?"
- Target: "Which PRD/story file should be treated as the source of truth?"
- Priority: "Which acceptance criteria are mandatory in this iteration?"
- PRD Versioning: "Which topic PRD should be version-bumped (for example `usage-limit-v3.md`)?"
- Delivery: "Do you want only docs updates now, or docs + implementation in one pass?"

## Output Contract

For every run, provide:

1. What command was executed (`update`, `create`, or `start`)
2. Files read
3. Files changed/created
4. Outstanding questions or risks
5. Recommended next step
