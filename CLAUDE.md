# CLAUDE.md — The Agency: AI Agent Repository

## What This Repo Is

A community-maintained collection of 144+ specialized AI agent personality files, organized into 15 domain categories. Each agent is a Markdown file with YAML frontmatter and structured sections that define a persona, workflow, and set of deliverables for an AI assistant to adopt.

The agents work natively with Claude Code (`~/.claude/agents/`) and GitHub Copilot, and ship conversion scripts to generate tool-specific formats for Cursor, Aider, Windsurf, Gemini CLI, OpenCode, Antigravity, OpenClaw, Qwen Code, and Kimi Code.

---

## Repository Structure

```
agency-agents/
├── academic/           # Scholars: anthropologist, geographer, historian, narratologist, psychologist
├── design/             # UI/UX designers, brand, visual storytelling, image prompts
├── engineering/        # Frontend, backend, DevOps, security, SRE, mobile, AI, data, etc.
├── finance/            # Bookkeeper, FP&A, financial analyst, investment researcher, tax strategist
├── game-development/   # Unity, Unreal, Godot, Blender, Roblox, game/level/audio design
├── marketing/          # Growth, SEO, social platforms (global + China-market), content
├── paid-media/         # PPC, programmatic, tracking, creative strategy, paid social
├── product/            # PM, sprint prioritization, feedback synthesis, trend research
├── project-management/ # Studio producer, project shepherd, Jira steward, ops
├── sales/              # Outbound, discovery, deal strategy, SE, proposals, coaching
├── spatial-computing/  # XR, visionOS, Metal, WebXR, cockpit interfaces
├── specialized/        # Unique cross-cutting agents (legal, healthcare, real estate, etc.)
├── strategy/           # NEXUS orchestration playbook, runbooks, coordination templates
├── support/            # Analytics, finance tracking, legal compliance, infra, exec summaries
├── testing/            # Evidence collector, reality checker, API tester, accessibility audit
├── examples/           # Multi-agent workflow examples and the Nexus Spatial Discovery exercise
├── integrations/       # READMEs for each tool integration (generated files are gitignored)
└── scripts/
    ├── lint-agents.sh  # Validates frontmatter + structure of agent files
    ├── convert.sh      # Generates tool-specific formats into integrations/
    ├── install.sh      # Installs agents/converted files to tool config dirs
    └── i18n/           # Localization helpers (zh-CN)
```

---

## Agent File Format

Every agent file must follow this structure exactly.

### Frontmatter (required fields: `name`, `description`, `color`)

```yaml
---
name: Agent Name
description: One-line description of the agent's specialty and focus
color: cyan                     # or "#hexcode"
emoji: 🎯                       # optional
vibe: One-line personality hook  # optional
services:                       # optional — only for agents that require external APIs
  - name: Service Name
    url: https://service-url.com
    tier: free                  # free | freemium | paid
---
```

### Body Sections

Sections are split into two semantic groups used by `convert.sh` to split agents into tool-specific formats (e.g., OpenClaw's SOUL.md / AGENTS.md split):

**Persona sections** (map to SOUL.md / identity):
- `## 🧠 Your Identity & Memory` — role, personality, background, memory
- `## 💭 Your Communication Style` — tone, voice, phrasing patterns
- `## 🚨 Critical Rules You Must Follow` — hard constraints and boundaries
- `## 🔄 Learning & Memory` — what the agent learns and remembers

**Operations sections** (map to AGENTS.md / capabilities):
- `## 🎯 Your Core Mission` — primary responsibilities with concrete deliverables
- `## 📋 Your Technical Deliverables` — code samples, templates, real output examples
- `## 🔄 Your Workflow Process` — step-by-step methodology (numbered phases)
- `## 🎯 Your Success Metrics` — specific measurable outcomes (with numbers)
- `## 🚀 Advanced Capabilities` — specialized techniques the agent masters

### File Naming Convention

```
<category>/<category>-<slug>.md
```

Examples: `engineering/engineering-frontend-developer.md`, `marketing/marketing-seo-specialist.md`

For subcategories (game engines, etc.), use subdirectories:
```
game-development/unity/unity-architect.md
game-development/godot/godot-gameplay-scripter.md
```

---

## Linting Rules

The CI workflow (`.github/workflows/lint-agents.yml`) runs `scripts/lint-agents.sh` on all changed agent files in a PR.

**Errors (block merge)**:
- Missing frontmatter opening `---`
- Empty or malformed frontmatter
- Missing any of: `name`, `description`, `color`

**Warnings (informational)**:
- Missing recommended sections: `Identity`, `Core Mission`, `Critical Rules`
- Body under 50 words
- No section headers mapping to SOUL.md (persona headers)
- No section headers mapping to AGENTS.md (operations headers)

To lint locally:
```bash
# Lint all agent files
./scripts/lint-agents.sh

# Lint specific files
./scripts/lint-agents.sh engineering/engineering-frontend-developer.md
```

---

## Integration Scripts

### `scripts/convert.sh`

Reads all agent `.md` files from the 15 category directories and generates tool-specific output into `integrations/<tool>/`. **Never commit these generated files** — they are all gitignored.

```bash
./scripts/convert.sh                    # regenerate all tools (serial)
./scripts/convert.sh --parallel         # regenerate all tools in parallel
./scripts/convert.sh --tool cursor      # regenerate one tool only
./scripts/convert.sh --parallel --jobs 8
```

Supported tools: `antigravity`, `gemini-cli`, `opencode`, `cursor`, `aider`, `windsurf`, `openclaw`, `qwen`, `kimi`, `all`

### `scripts/install.sh`

Interactive or scripted installer that copies converted/source files to user tool config directories. Auto-detects installed tools.

```bash
./scripts/install.sh                            # interactive, auto-detect
./scripts/install.sh --tool claude-code         # install directly to ~/.claude/agents/
./scripts/install.sh --no-interactive --tool all
./scripts/install.sh --no-interactive --parallel
```

---

## What Is and Isn't Committed

**Committed**:
- All source agent `.md` files in the 15 category directories
- `scripts/` (lint, convert, install scripts)
- `integrations/*/README.md` files
- `examples/`, `strategy/`, `.github/`

**Gitignored (never commit)**:
- `integrations/antigravity/agency-*/`
- `integrations/gemini-cli/skills/` and `gemini-extension.json`
- `integrations/opencode/agents/`
- `integrations/cursor/rules/`
- `integrations/aider/CONVENTIONS.md`
- `integrations/windsurf/.windsurfrules`
- `integrations/openclaw/*` (except README.md)
- `integrations/qwen/agents/`
- `integrations/kimi/*/`

---

## Development Workflows

### Adding a New Agent

1. Choose the appropriate category directory (or `specialized/` for cross-cutting roles)
2. Name the file `<category>-<slug>.md`
3. Include required frontmatter: `name`, `description`, `color`
4. Include recommended sections: Identity, Core Mission, Critical Rules
5. Add at least 2-3 concrete code/template examples in the Technical Deliverables section
6. Define specific, measurable success metrics (with numbers)
7. Run `./scripts/lint-agents.sh <your-file.md>` and fix any errors before submitting a PR

### Modifying an Existing Agent

- Content improvements (examples, metrics, workflows, typos): submit a PR directly
- Bulk reformatting or changes touching many files: open a Discussion first to avoid merge conflicts

### Architectural Changes (new scripts, CI, directories)

Open a GitHub Discussion before writing code. This prevents conflicting approaches and saves time.

---

## Agent Design Principles

1. **Narrow, deep specialization** — one domain, done well. Avoid "jack of all trades" agents.
2. **Distinct personality** — not "I'm a helpful assistant." Give the agent voice, constraints, and character.
3. **Concrete deliverables** — real, runnable code examples (not pseudo-code). At least 2-3 per agent.
4. **Measurable success** — specific numbers: "page load under 3s on 3G", "10,000+ combined karma".
5. **Proven workflows** — step-by-step phases, not vague guidance.
6. **Useful without its services** — if an agent depends on external APIs, strip them and a viable persona should remain. Agents are for the user, not vendor quickstart guides.

### Avoid

- Generic "I will help you with..." descriptions
- No code examples or deliverables
- Overly broad scope
- Theoretical approaches that haven't been tested in real scenarios
- Documenting what frontmatter fields do inside the agent body

---

## PR Conventions

**Branch naming**: `add-<agent-slug>` or `improve-<agent-slug>`

**Commit message format**: `Add [Agent Name] specialist` or `Improve [Agent Name]: <what changed>`

**PR title format**: `Add [Agent Name] - [Category]`

**PR checklist** (from `.github/PULL_REQUEST_TEMPLATE.md`):
- [ ] Follows agent template structure
- [ ] Includes personality and voice
- [ ] Has concrete code/template examples
- [ ] Defines success metrics
- [ ] Includes step-by-step workflow
- [ ] Proofread and formatted correctly
- [ ] Tested in real scenarios

---

## Strategy Directory

`strategy/` contains high-level orchestration documentation, not agent files:

- `nexus-strategy.md` — NEXUS multi-agent coordination doctrine (7-phase deployment model)
- `QUICKSTART.md` — Quick-start activation guide
- `EXECUTIVE-BRIEF.md` — Executive summary of NEXUS
- `playbooks/phase-*.md` — Per-phase playbooks (discovery → operate)
- `runbooks/scenario-*.md` — Scenario-specific runbooks (startup MVP, incident response, etc.)
- `coordination/` — Agent activation prompts and handoff templates

These files are not agent definitions and are not processed by `convert.sh` or `lint-agents.sh`.

---

## Examples Directory

`examples/` contains multi-agent workflow demonstrations:

- `nexus-spatial-discovery.md` — 8 agents deployed simultaneously on a single product opportunity
- `workflow-*.md` — Single-scenario workflow walkthroughs (book chapter, landing page, startup MVP, memory-enabled workflow)
- `README.md` — Index of example files

These are reference material, not agent definitions.

---

## Key Invariants for AI Assistants

- **Never commit generated integration files.** They live in gitignored paths under `integrations/` and are produced by `scripts/convert.sh`.
- **Lint before suggesting a PR is ready.** Run `./scripts/lint-agents.sh` and fix all errors.
- **One file per new agent PR.** The sweet spot for contribution is a single `.md` file.
- **Frontmatter is required.** `name`, `description`, and `color` must be present or CI will fail.
- **The `strategy/` directory is documentation, not agents.** Do not lint or convert these files.
- **Qwen agents support `${variable}` templating** in the body for dynamic context injection.
- **Section header keywords determine conversion routing.** Headers containing `identity`, `learning.*memory`, `communication`, `style`, `critical.rule`, or `rules you must follow` map to SOUL.md. All other `##`-level headers map to AGENTS.md.
