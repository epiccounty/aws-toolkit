---
name: aws-security
description: >-
  PII/sensitive data identification and handling, destructive command control, and
  the secret-entry protocol. Applies to every aws-* skill. Triggers: "sensitive data",
  "민감정보", "pii", "secret", "시크릿", "password", "비밀번호", "delete", "삭제",
  "destructive", "파괴적", "secrets manager".
---

# AWS Security Rules (common to every aws-* task)

## 1. Identifying sensitive data

Never expose the following in agent output, summaries, code, commits, or logs:

| Class | Examples |
|-------|----------|
| Credentials | access keys (`AKIA...`), secret keys, tokens (`Bearer ...`), pem contents |
| PII | names, emails, phone numbers, national IDs, bank/card numbers, full home paths (`~/...`) |
| Infrastructure | DB connection strings, `.env` values, internal domains, SSH host keys |
| AWS | Secrets Manager / Parameter Store plaintext, credential report raw content |

On detection: replace with `<REDACTED>` or a placeholder and add a one-line warning.
When CLI output mixes them in, exclude the field via `--query` before summarizing.

## 2. Secret-entry protocol (hard rule for agents)

**Principle: secret values never exist anywhere the agent can see — not in suggested
commands, not in output, not in the transcript.**

1. Never ask the user to paste a secret into chat — agents must not request it, users must not do it.
2. Suggested commands carry **references only** (`$SECRET_ID`, `--secret-id`, an env var
   name) — never values.
3. When a command needs a real value: hand over a template and **the user runs it in
   their own terminal**:
   - The `! <command>` prefix is also forbidden — its input and output are visible to the agent in the session transcript
   - The user reports back only success/failure
4. Secrets Manager lookups follow the same rule: the agent must never receive the output
   of `get-secret-value`. The user runs it themselves, or the application runtime decrypts.
5. Writing `.env`/config files: the agent writes key names with placeholders only; the
   user fills values in an editor.

Rationale: a secret that enters the transcript cannot be removed — it persists in
caches, logs, and summaries.

## 3. Destructive command control

Targets: `terminate/delete/rb/rm/detach`, `put-*` (overwrites), `update* --replace`,
IAM policy edits, `cloudformation delete-stack`, `s3api delete-objects` — anything
irreversible.

Procedure (order is mandatory):
1. **Impact list first**: before deleting, output the affected resource list, count, and
   names (`describe`/`list` + `--query`)
2. **Dry-run whenever available**: `--dry-run`
3. **Explicit approval**: never execute without showing the list — not "delete?" but
   "about to delete the 3 resources below — approve?"
4. **Minimum scope**: no `--all`, no wildcards, no unconditioned `delete-objects` —
   explicit IDs/names only
5. **Verify after execution**: re-query and report the result

For IAM/security-group changes, snapshot the current values (json output) to a file
before modifying — that is the rollback record.

## 4. Where these rules apply

- Main thread: follow this skill
- aws-ops subagent: the same rules are embedded in agents/aws-ops.md (skill loading is
  not guaranteed inside agents)
- The SSO profile itself is effectively the secret-avoidance mechanism — if something
  demands long-lived keys, refuse and propose the SSO path instead
