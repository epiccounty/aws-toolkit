# AGENTS.md — aws-toolkit

> Shared agent guide. Claude Code, Codex, agy, and hermes all load this file.
> Agent-facing docs are English; the human-facing README is bilingual (EN/KO).

## Role

Integrated AWS access for the epiccounty account: SSO login, SSM instance sessions
and port-forward tunnels, general AWS CLI operations, security guardrails,
onboarding, and an account-wide service map.

Authoritative sources live in `skills/<name>/SKILL.md` (one per skill). Host-discovery
copies under `.claude/skills/`, `.codex/skills/`, and `.hermes/skills/` are symlinks
that must mirror them — run `forge.py doctor <path> --fix` after any skill edit.

## Rules that override everything

- Security rules in `skills/aws-security/SKILL.md` apply to every AWS operation,
  including inside the aws-ops subagent (embedded in `agents/aws-ops.md`).
- Secret values never enter commands, output, or transcripts. Placeholder templates
  only; the user runs secret-bearing commands in their own terminal.
- Destructive commands: impact list → dry-run → explicit approval → minimum scope → verify.

## Host differences

- **Claude Code**: loads `skills/` (via `.claude-plugin/plugin.json`), `agents/aws-ops.md`,
  and `commands/` (slash commands — currently unused).
- **Codex / agy / hermes**: no `commands/` support — follow SKILL.md intent→action tables.
