# Skills

This directory contains [Agent Skills](https://agentskills.io/) for the
tekton-pruner project. Skills are loaded by AI agents to provide specialized,
project-specific knowledge and workflows.

## Available Skills

| Skill | Description |
|-------|-------------|
| [local-development](./local-development/SKILL.md) | Bootstrap a kind cluster, deploy the operator, set up observability |
| [running-tests](./running-tests/SKILL.md) | Run unit tests, linters, and e2e tests |
| [sync-plumbing-workflows](./sync-plumbing-workflows/SKILL.md) | Resolve latest SHA from tektoncd/plumbing and update workflow digest pins |
| [code-review](./code-review/SKILL.md) | Review PRs against Tekton and operator quality standards |
| [filing-issues](./filing-issues/SKILL.md) | File well-crafted GitHub issues for bugs and features |
| [pruner-config](./pruner-config/SKILL.md) | Author, validate, and debug pruner ConfigMap configuration |

## Agent Client Locations

Skills are maintained here (`skills/`) as the single source of truth.
Each supported agent client finds them via a symlink in its config directory:

| Agent | Config dir | Symlink |
|-------|-----------|---------|
| [Claude Code](https://code.claude.com/docs/en/skills) | `.claude/` | `.claude/skills` → `../skills` |
| Any compliant client | `.agents/` | `.agents/skills` → `../skills` |

`.agents/skills/` is the [cross-client interoperability convention](https://agentskills.io/client-implementation/adding-skills-support.md)
defined by the Agent Skills specification — all compliant agents scan it automatically.
`.claude/skills/` is Claude Code's native lookup path.

## Format

Skills follow the [Agent Skills specification](https://agentskills.io/specification).
Each skill is a directory containing a `SKILL.md` with YAML frontmatter and
Markdown instructions. Agents load only the `name` and `description` at startup;
the full instructions are loaded only when a task matches.

## Adding a Skill

1. Create `skills/<skill-name>/SKILL.md`
2. Add YAML frontmatter with at minimum `name` and `description`
3. Write clear, step-by-step instructions in the Markdown body
4. Add an entry to the table above
5. Optionally add `scripts/`, `references/`, or `assets/` subdirectories
