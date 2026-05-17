# CLAUDE.md — Agency Agents Codebase Guide

This file provides AI assistants with a complete understanding of the agency-agents repository: what it is, how it's structured, and how to work with it correctly.

---

## What This Repository Is

**Agency Agents** is a curated collection of 169+ specialized AI agent personality definitions, organized into 14 divisions (e.g., engineering, marketing, design). Each agent is a standalone Markdown file that defines a persona — identity, communication style, workflows, deliverables, and success metrics.

This is **not a runtime application**. There is no Node.js app, no Python service, no compiled binary. The agents are structured prompt templates compatible with multiple AI coding tools.

**Supported tools** (via conversion and installation scripts):
- Claude Code (`~/.claude/agents/`)
- GitHub Copilot (`~/.github/agents/`)
- Antigravity, Gemini CLI, OpenCode, Cursor, Aider, Windsurf, OpenClaw, Qwen Code, Kimi Code

---

## Repository Structure

```
agency-agents/
├── academic/               # Scholarly/research agents
├── design/                 # UX, UI, and creative specialists
├── engineering/            # Software development engineers (29 agents)
├── finance/                # Financial planning and accounting specialists
├── game-development/       # Game design and development agents
├── marketing/              # Growth and marketing specialists (30 agents)
├── paid-media/             # Paid acquisition agents
├── product/                # Product management specialists
├── project-management/     # PM and coordination agents
├── sales/                  # Sales specialists
├── spatial-computing/      # AR/VR/XR agents
├── specialized/            # Unique specialists that don't fit elsewhere (41 agents)
├── strategy/               # Strategic advisory agents
├── support/                # Operations and support agents
├── testing/                # QA and testing agents
├── examples/               # Multi-agent workflow examples
├── integrations/           # Generated tool-specific output (do NOT edit directly)
├── scripts/                # Build, conversion, and installation scripts
│   ├── convert.sh          # Converts agents to tool-specific formats
│   ├── install.sh          # Installs converted agents to local tool config dirs
│   ├── lint-agents.sh      # Validates agent file structure (also runs in CI)
│   └── i18n/
│       └── agent-names-zh.json   # Chinese translations for agent names
├── .github/
│   ├── workflows/lint-agents.yml # CI: validates changed agent files on PRs
│   └── PULL_REQUEST_TEMPLATE.md
├── CONTRIBUTING.md
├── SECURITY.md
├── README.md
└── CLAUDE.md               # This file
```

### Key rule: `integrations/` is generated

Files inside `integrations/` (except `README.md` files and scripts) are produced by `scripts/convert.sh`. Never edit them directly — they will be overwritten. The `.gitignore` excludes most generated output.

---

## Agent File Format

Every agent lives in its category directory, named `<category>-<specialty>.md` in kebab-case.

**Example:** `engineering/engineering-frontend-developer.md`

### Required YAML Frontmatter

```yaml
---
name: Frontend Developer
description: Expert frontend developer specializing in modern web technologies, React/Vue/Angular frameworks, UI implementation, and performance optimization
color: cyan
emoji: 🖥️
vibe: Builds responsive, accessible web apps with pixel-perfect precision.
---
```

**Required fields** (CI will fail without them):
- `name` — Human-readable agent name
- `description` — One sentence describing expertise
- `color` — UI accent color (e.g., `cyan`, `blue`, `green`, `orange`, `red`, `purple`)

**Optional fields:**
- `emoji` — Representative emoji
- `vibe` — Short personality tagline (one sentence)
- `services` — List of external service dependencies (name, url, tier)

### Body Structure

After the frontmatter, the agent body follows this section pattern:

**Persona sections** (classified as `SOUL.md` in OpenClaw conversion):
- `## 🧠 Your Identity & Memory` — Role, personality traits, background
- `## 💭 Your Communication Style` — Tone, voice, phrases
- `## 🚨 Critical Rules You Must Follow` — Hard constraints, non-negotiables

**Operations sections** (classified as `AGENTS.md` in OpenClaw conversion):
- `## 🎯 Your Core Mission` — Primary responsibilities and goals
- `## 📋 Your Technical Deliverables` — Code samples, templates, outputs
- `## 🔄 Your Workflow Process` — Step-by-step methodology
- `## 🎯 Your Success Metrics` — Quantifiable outcomes
- `## 🚀 Advanced Capabilities` — Specialized techniques

The lint script uses these patterns to classify sections. At minimum, each agent needs at least one "soul" section (Identity, Communication, Critical Rules) and at least one "operations" section (everything else).

### Content Conventions

- **Code examples must be production-grade** — Full, runnable code (not pseudo-code), modern patterns (TypeScript, async/await, proper types)
- **Success metrics must be quantified** — "Page load under 3 seconds", not "fast page loads"
- **Communication style must be distinct** — Each agent has a unique voice, not generic corporate language
- **Workflows are numbered phases** — Step-by-step, not vague guidance
- **Section headers use emoji** — For visual scanning in supported tools

---

## Development Workflows

### Adding a New Agent

1. Choose the correct category directory (see `CONTRIBUTING.md` for category descriptions)
2. Create `<category>/<category>-<specialty>.md`
3. Use the required frontmatter fields (`name`, `description`, `color`)
4. Include at least one "soul" section and one "operations" section
5. Add production-quality code examples if applicable
6. Run the linter to validate before committing:
   ```bash
   ./scripts/lint-agents.sh engineering/engineering-my-new-agent.md
   ```
7. Test the agent in a real tool scenario
8. Submit PR — CI will auto-lint changed agent files

### Converting Agents to Tool Formats

```bash
# Convert all agents to all supported tool formats
./scripts/convert.sh

# Convert for a specific tool only
./scripts/convert.sh --tool cursor
./scripts/convert.sh --tool antigravity
./scripts/convert.sh --tool openclaw

# Parallel conversion (faster on multi-core machines)
./scripts/convert.sh --parallel --jobs 8
```

Output goes to `integrations/<tool>/` — these are excluded from git (except READMEs).

### Installing Agents Locally

```bash
# Interactive mode — auto-detects installed tools, shows checkbox UI
./scripts/install.sh

# Install for a specific tool
./scripts/install.sh --tool claude-code
./scripts/install.sh --tool cursor

# Install for all detected tools non-interactively
./scripts/install.sh --no-interactive

# Parallel installation
./scripts/install.sh --parallel
```

### Linting Agent Files

```bash
# Lint all agent files
./scripts/lint-agents.sh

# Lint specific files
./scripts/lint-agents.sh engineering/engineering-frontend-developer.md

# Lint an entire category
./scripts/lint-agents.sh engineering/*.md
```

Exit code 0 = PASSED, 1 = errors found. Warnings do not cause failure.

---

## CI/CD

**Workflow:** `.github/workflows/lint-agents.yml`

Triggers on PRs that modify any `.md` file inside agent category directories. It:
1. Identifies which agent files changed
2. Runs `scripts/lint-agents.sh` on only the changed files
3. Fails the PR if any ERROR-level lint issue is found

**What triggers CI:** Changes to files in `academic/`, `design/`, `engineering/`, `finance/`, `game-development/`, `marketing/`, `paid-media/`, `product/`, `project-management/`, `sales/`, `spatial-computing/`, `specialized/`, `strategy/`, `support/`, `testing/`

**What does NOT trigger CI:** Changes to `scripts/`, `integrations/`, `examples/`, documentation files

---

## Lint Rules Reference

**Errors (block merge):**
- Missing frontmatter opening `---`
- Empty or malformed frontmatter
- Missing `name`, `description`, or `color` field

**Warnings (do not block merge):**
- Missing recommended section: `Identity`
- Missing recommended section: `Core Mission`
- Missing recommended section: `Critical Rules`
- Body has fewer than 50 words
- No "soul" section headers (Identity/Communication/Critical Rules)
- No "operations" section headers (Mission/Deliverables/Workflow/etc.)

---

## Section Classification (for OpenClaw/convert.sh)

The `classify_header_target` function in `scripts/lint-agents.sh` (and the equivalent logic in `scripts/convert.sh`) maps section headers to either `soul` or `agents`:

**Soul sections** (case-insensitive match):
- Contains "identity"
- Contains "learning" AND "memory"
- Contains "communication"
- Contains "style"
- Contains "critical rule" or "rules you must follow"

**Agents sections:** everything else

This determines how `scripts/convert.sh` splits content for the OpenClaw workspace format (SOUL.md + AGENTS.md + IDENTITY.md).

---

## Naming Conventions

| Thing | Convention | Example |
|-------|-----------|---------|
| Agent file | `<category>-<specialty>.md` | `engineering-frontend-developer.md` |
| Agent `name` field | Title Case | `Frontend Developer` |
| Category directory | kebab-case | `game-development/` |
| Script files | kebab-case `.sh` | `lint-agents.sh` |
| Integration output | `integrations/<tool>/` | `integrations/cursor/` |

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Agent definitions | Markdown with YAML frontmatter |
| Build/conversion | Bash (POSIX-compatible, Bash 3.2+) |
| CI | GitHub Actions |
| i18n | JSON (zh-CN agent name translations) |
| Runtime deps | None — agents are prompt text |

Standard Unix tools used in scripts: `find`, `grep`, `awk`, `sed`, `rsync`, `mkdir`, `sort`, `wc`

---

## Adding a New Tool Integration

To add support for a new AI coding tool, modify `scripts/convert.sh`:

1. Add a new `convert_<toolname>()` function following the pattern of existing converters
2. Register it in the main dispatch (`--tool` flag handling)
3. Add the tool to the `all` target in the parallel section
4. Add an entry in `integrations/<toolname>/README.md` explaining the format
5. Update `scripts/install.sh` with the install target directory and copy logic
6. Document the new tool in `README.md`

---

## Security Notes (from SECURITY.md)

- Agent `.md` files are **non-executable prompt text** — they pose no code execution risk
- Scripts (`scripts/*.sh`) accept user input for file paths — validate before extending
- Never commit API keys, credentials, or tokens into agent files
- Report vulnerabilities via GitHub Security tab, not public issues

---

## Examples

The `examples/` directory contains multi-agent workflow demonstrations:

- `nexus-spatial-discovery.md` — 8 agents collaborating: market analysis, technical architecture, brand strategy, GTM, UX research, project timeline
- `workflow-startup-mvp.md` — Building a startup MVP with multiple agents
- `workflow-book-chapter.md` — Co-authoring a book chapter
- `workflow-landing-page.md` — Building landing pages
- `workflow-with-memory.md` — Using MCP memory systems with agents

These show how agents can be chained: one agent's output becomes another's input.

---

## Common Mistakes to Avoid

1. **Editing files in `integrations/`** — They are generated and will be overwritten by `convert.sh`
2. **Skipping frontmatter** — CI will fail without `name`, `description`, and `color`
3. **Vague success metrics** — "make it better" is not a metric; use measurable numbers
4. **Generic code examples** — Pseudo-code or trivial snippets reduce agent usefulness
5. **Wrong category** — Put truly unique specialists in `specialized/`, not a forced category fit
6. **Missing section classification** — Ensure at least one "soul" and one "operations" section header so convert.sh can split content correctly
