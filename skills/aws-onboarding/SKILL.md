---
name: aws-onboarding
description: >-
  Onboarding for users without SSO/SSM set up — tool install, ~/.aws/config setup,
  first login, first instance session. Triggers: "onboarding", "aws setup",
  "온보딩", "aws 설정", "신규 설정", "처음 설정", "sso 설정", "ssm 설정 방법".
---

# AWS Onboarding (new users)

Preconditions: the epiccounty account already has Identity Center enabled and
`ssm-instance-role` exists. Follow the steps below on a new machine or as a new user.

## 1. Install tools (macOS)

```bash
brew install awscli                          # AWS CLI v2
brew install --cask session-manager-plugin   # for SSM sessions
aws --version && session-manager-plugin --version   # verify
```

## 2. ~/.aws/config setup

Get 3 values from the administrator: start URL, account ID (12 digits), role name.
Append:

```ini
[profile epiccounty]
sso_session = epiccounty
sso_account_id = <ACCOUNT_ID>
sso_role_name = <ROLE_NAME>
region = ap-northeast-2
output = json

[sso-session epiccounty]
sso_start_url = <START_URL>
sso_region = ap-northeast-2
sso_registration_scopes = sso:account:access
```

Coexists with any existing `[sso-session]` blocks — no conflict.

## 3. Login and verify

```bash
aws sso login --profile epiccounty
aws sts get-caller-identity --profile epiccounty   # check Account/Arn
```

If the Identity Center user does not exist yet, the administrator must run the
account-side steps first (below).

## 4. First SSM session test

```bash
aws ssm describe-instance-information --profile epiccounty \
  --query 'InstanceInformationList[].[InstanceId,PingStatus]' --output table
aws ssm start-session --target <INSTANCE_ID> --profile epiccounty
```

Success completes onboarding. Day-to-day usage: see the aws-ssm skill.

## Account-side preparation (administrator — new Identity Center users)

1. IAM Identity Center console — Users, create a user (email)
2. Permission sets — reuse the existing `AdministratorAccess` or create one
3. AWS accounts — assign user + permission set to the account
4. User accepts the invite email, sets password/MFA
5. Hand the user the start URL, account ID, role name (for step 2)

## Common mistakes

| Symptom | Cause |
|---------|-------|
| Browser opens, then "You are not authorized" | Permission set not assigned — admin, step 3 |
| `ProfileNotFound` | `[profile epiccounty]` exists but ran with `--profile default` |
| `session-manager-plugin not found` | Terminal not reopened after cask install |
| Instance list empty | `ssm-instance-role` not attached — see aws-ssm troubleshooting |
