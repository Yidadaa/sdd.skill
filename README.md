# sdd.skill
Story-Driven Development: split PRDs into stories, implement them end-to-end, and review code quality.

AI 没有读心术，精准描述你的需求，AI 会帮你完成剩下的。

[中文文档](README.zh-CN.md)

## What It Does

This skill adds four commands to your AI coding agent:

| Command  | Purpose                                                            |
| -------- | ------------------------------------------------------------------ |
| `update` | Revise existing PRDs or stories based on new requirements          |
| `create` | Split a PRD into independently implementable stories               |
| `start`  | Implement a story end-to-end (code, tests, docs)                   |
| `review` | Review staged git changes for type safety, style, and code quality |

## Why Story-Driven?

Large features are hard to ship in one pass. This skill enforces a disciplined flow:

1. **Write a PRD** — capture requirements in `docs/prd/`.
2. **Create stories** — the skill breaks the PRD into small, independently deliverable stories in `docs/story/`.
3. **Implement one story at a time** — the agent reads the story, writes code, runs tests, and marks it done.
4. **Review before commit** — catch type issues, magic numbers, duplicated logic, and style violations.

Each story file follows a consistent structure: goal, acceptance criteria, functional points, and test case designs — all in natural language, no code in story files.

## Installation

### Automatic
Copy and send the following prompt to your AI coding agent:

> Download https://raw.githubusercontent.com/Yidadaa/sdd.skill/main/story/SKILL.md and save it to `.agents/skills/story/SKILL.md` in this project.

### Manual
**Linux / macOS:**

```bash
mkdir -p .agents/skills/story && curl -fsSL -o .agents/skills/story/SKILL.md https://raw.githubusercontent.com/Yidadaa/sdd.skill/main/story/SKILL.md
```

**Windows (PowerShell):**

```powershell
New-Item -ItemType Directory -Force -Path .agents\skills\story | Out-Null; Invoke-WebRequest -Uri "https://raw.githubusercontent.com/Yidadaa/sdd.skill/main/story/SKILL.md" -OutFile ".agents\skills\story\SKILL.md"
```

The skill is automatically picked up by AI coding agents that support custom skills.

## Usage

In your AI coding agent, reference the skill naturally:

```
# Create stories from a PRD
/story create stories from docs/prd/my-feature.md

# Start implementing a specific story
/story start story 2.1

# Update a story with new requirements
/story update story 3.2 — add pagination support to the list API

# Review staged changes
/story review my staged changes
```

## File Conventions

### PRD Files

```
docs/prd/<topic>.md          # initial version
docs/prd/<topic>-v2.md       # revised version (old version is preserved)
```

### Story Files

```
docs/story/<M>.<N>-<status>.md
```

- `M` — main story number (starts from 1)
- `N` — sub-story number (0 = overview, 1+ = sub-stories)
- `status` — `plan` | `wip` | `done`

Examples:
- `1.0-plan.md` — Main story 1 overview
- `1.1-wip.md` — Sub-story 1 of main story 1 (in progress)
- `1.2-done.md` — Sub-story 2 of main story 1 (completed)

### Story File Structure

Every story file contains these sections:

```markdown
# Story M.N — Short Title

## Main Goal
What this story aims to achieve.

## Acceptance Criteria
- [ ] Criterion 1
- [ ] Criterion 2

## Functional Points
Natural-language description of what to implement.

## Unit Test Case Design
Test scenarios described in natural language.

## Behavior Test Case Design
End-to-end test scenarios described in natural language.
```

## Review Checks

The `review` command analyzes staged diffs for:

- **Type safety** — no `as any`, no broad `Record<string, any>`, prefer type inference over redefinition
- **Redundant logic** — extract repeated code, consolidate copy-pasted branches
- **Magic values** — no hard-coded numbers, route strings, or identifiers
- **Code style** — no inline styles, no empty catch blocks, no console.log in production

## Customization

The SKILL.md file is self-contained. You can adapt it to your project by:

- Editing the review rules to match your team's coding standards
- Adjusting the story file structure to fit your workflow
- Adding project-specific clarification question templates
- Changing file path conventions (`docs/prd/`, `docs/story/`)

## Requirements

- Any AI coding agent that supports custom skills

## License

MIT
