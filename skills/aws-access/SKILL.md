---
name: aws-access
description: >-
  AWS SSO login, profile management, and session expiry handling for the epiccounty
  account. Triggers: "sso login", "aws login", "aws 로그인", "session expired",
  "세션 만료", "aws profile", "expired token".
---

# AWS Access (SSO)

## Profiles

| Profile | Purpose |
|---------|---------|
| `epiccounty` | Personal account, AdministratorAccess (Identity Center) |

Always pass `--profile` explicitly. Account IDs and start URLs live in `~/.aws/config` —
never hardcode them in documents or code.

## Login

```bash
# Login (browser authentication)
aws sso login --profile epiccounty

# Session check — Account/Arn printed means OK
aws sts get-caller-identity --profile epiccounty
```

## Session expiry

Symptoms: `Token has expired`, `ExpiredTokenException`, `The SSO session has expired`,
`SsoSessionNotFound`. Fix: re-run `aws sso login`. Before any unattended script run,
verify the session with `get-caller-identity` first.

## Adding a new account

Append a `[profile <name>]` + `[sso-session <name>]` pair to `~/.aws/config`:
sso_session, sso_account_id, sso_role_name / sso_start_url, sso_region,
`sso_registration_scopes = sso:account:access`. Verify with `aws sso login --profile <name>`.
Full walkthrough: see the aws-onboarding skill.
