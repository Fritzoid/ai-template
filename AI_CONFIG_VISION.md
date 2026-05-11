# AI Config Vision

## What This Is

A declarative configuration management tool for AI tooling — in the spirit of Ansible, but for AI. You define the desired state of AI configuration across machines, projects, and tasks, and the tool converges reality toward that state.

## Why This Matters

The files that guide AI tools — instruction files, rules, plugin configs, MCP server setups — have an outsized influence on the quality of AI output. Managing these manually doesn't scale. As organizations adopt AI across developer machines, CI runners, autonomous agents, and other devices, this configuration becomes infrastructure that needs the same rigor as any other.

## Core Principles

- **Declarative** — Define desired state, not steps to get there.
- **Idempotent** — Running it twice changes nothing if state already matches.
- **Composable** — Build configs from small, reusable pieces. No rigid archetypes.
- **GitOps** — A git repo is the source of truth. Changes go through PRs, get reviewed, and leave an audit trail.

## What It Manages

### AI Tool Configuration Surfaces

- **Claude Code** — `CLAUDE.md` files (project, nested, global `~/.claude/CLAUDE.md`), `settings.json` (project and user level), MCP server configs.
- **Cursor** — `.cursorrules`, `.cursor/rules/` directory, `.cursor/mcp.json`.
- **GitHub Copilot** — `.github/copilot-instructions.md`, `.github/copilot/` directory.
- **Aider** — `.aider.conf.yml`, `.aiderignore`.
- **Windsurf / Codeium** — `.windsurfrules`.
- **General** — `.env` files read by AI tools, `.gitignore` patterns for AI artifacts, MCP server configurations.
- **More to come** — Any tool or device that runs AI and can be configured through files, environment, or settings.

## How It Works

### The GitOps Repo

A single org-wide repository serves as the source of truth for all AI configuration. The repo is a library of text snippets organized by labels. Directory structure maps to labels, directories contain text snippets. When composing config for a given context, the tool collects snippets from relevant directories and assembles them.

```
ai-config/
  base/
  languages/
    base/
    rust/
    python/
    typescript/
  tools/
    base/
    claude-code/
    cursor/
    copilot/
    aider/
  domains/
    base/
    ml/
    web/
    infrastructure/
    security/
  org/
    base/
    code-style/
    security-policies/
    review-guidelines/
  machines/
    developer/
    ci-runner/
    agent-container/
    gpu-server/
  projects/
    api-service/
    web-app/
    ml-pipeline/
```

Labels map directly to directories. `base/` at any level is just another label — no special behavior. If you want it, include it.

**Example composition:** A task labeled `base` + `languages/base` + `languages/rust` + `tools/claude-code` + `domains/security` collects all the snippets from those directories.

### Composable Capabilities, Not Roles

No one gets pigeonholed into a job title. Instead, capabilities are building blocks you enable or disable:

- A backend developer enables: `rust`, `python`, `claude-code`, `security`.
- A data scientist enables: `python`, `ml`, `cursor`.
- A full-stack jack-of-all-trades enables: whatever they need.
- A CI runner enables: `claude-code`, minimal project rules.
- An autonomous agent container gets capabilities assigned by the orchestration system based on task labels.

### Three Contexts of Application

1. **Machine setup** — A developer (or device) gets global AI tool settings based on enabled capabilities. Part of initial machine provisioning.
2. **Project clone** — When a repo is checked out, project-appropriate AI configs are applied.
3. **Task assignment** — An autonomous agent receives a task with labels. The tool composes the right instruction files for that specific run. Labels like `security-sensitive`, `refactor`, `greenfield`, `payments-team` each contribute config fragments.

### Convergence

- Checks current state against desired state.
- Shows diffs before applying.
- Supports dry-run / check mode for drift detection.
- Applies changes to reach desired state.

### AI-Assisted Conflict Detection

When multiple capabilities or labels are composed together, AI reviews the merged configuration for contradictions and incoherence:

- **At authoring time** — PRs to the config repo get AI-assisted review. "This new rule contradicts an existing security policy."
- **At composition time** — When labels combine for a task, the merged result is checked before being applied. "These labels produce conflicting guidance on testing strategy."
- **Suggestions** — Not just flags problems but proposes resolutions with reasoning.

## Target Environments

This is not limited to developer laptops. Anything that runs AI and needs configuration:

- Developer machines (laptop, desktop)
- CI/CD runners
- Autonomous agent containers
- GPU servers running local models
- Edge devices
- Shared development servers

## Relationship to Workinabox

This tool is the **AI configuration layer**. Workinabox handles broader machine provisioning and agent orchestration. The integration point:

- Workinabox's task system assigns labels to tasks.
- Labels drive which capabilities get composed for an agent's configuration.
- This tool materializes the right instruction files for the agent before it starts work.
- Workinabox may call this tool as a library or CLI during agent container setup.

## Auditability

Because config is GitOps-managed and agents are configured through this tool:

- Every agent run can be traced back to the exact config repo commit that produced its instructions.
- If an agent produces a bad PR, you can inspect exactly what guidance it was given.
- Config changes can be rolled back like any other deployment.

## Open Questions

None currently.
