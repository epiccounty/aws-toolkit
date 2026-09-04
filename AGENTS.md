# AGENTS.md — aws-toolkit

> Shared agent guide. Claude Code, Codex, agy, and hermes all load this file.
> Agent-facing docs are English; the human-facing README is bilingual (EN/KO).

## Role

Integrated AWS access for the epiccounty account: SSO login, SSM instance sessions
and port-forward tunnels, general AWS CLI operations, security guardrails,
onboarding, and an account-wide service map.

Authoritative sources live in `skills/<name>/SKILL.md` (one per skill). Host-discovery
trees `.claude/skills/`, `.codex/skills/`, `.hermes/skills/`, and `.claude/agents/` are
**directory symlinks** to `../skills` / `../agents`, so a skill edit shows up on every
host with no mirror step. After structural changes run `forge.py doctor <path> --fix`
(ships with the **plugin-forge** plugin: `scripts/forge.py` in its marketplace cache —
`python3 ~/.claude/plugins/marketplaces/plugin-forge/scripts/forge.py doctor . --owner epiccounty --fix`).
Review its diff before committing: `--fix` also scaffolds the Codex catalog
(`.agents/plugins/marketplace.json` + `plugins/aws-toolkit/` dirlinks).

## Rules that override everything

- Security rules in `skills/aws-security/SKILL.md` apply to every AWS operation,
  including inside the aws-ops subagent (embedded in `agents/aws-ops.md`).
- Secret values never enter commands, output, or transcripts. Placeholder templates
  only; the user runs secret-bearing commands in their own terminal.
- Destructive commands: impact list → dry-run → explicit approval → minimum scope → verify.

## Host differences

- **Claude Code**: loads `skills/` and `agents/aws-ops.md` (via `.claude-plugin/plugin.json`).
  No `commands/` directory — add one together with the manifest entry if slash commands
  are ever needed.
- **Codex / agy / hermes**: no `commands/` support — follow SKILL.md intent→action tables.

## Validation

`python3 ~/.claude/plugins/marketplaces/plugin-forge/scripts/forge.py doctor . --owner epiccounty`
(read-only), `agy plugin validate .`, `hermes plugins doctor <local-copy>`, plus
`.github/workflows/ci.yml` (manifest/frontmatter/symlink checks, ruff).

## License

Apache-2.0 (matches the workspace convention).
