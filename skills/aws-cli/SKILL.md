---
name: aws-cli
description: >-
  General AWS CLI usage patterns — output control, common queries, cost checks,
  safety rules. Triggers: "aws cli", "aws query", "aws 조회", "resource list",
  "리소스 목록", "aws cost", "aws 비용", "aws logs".
---

# AWS CLI (general)

Default profile `epiccounty` (`ap-northeast-2`, output json). Override `--region` as needed.

## Output control

- Lists read best with `--output table` + `--query`
- Pipe to scripts with `--output json` + `jq`
- Large result sets: `--no-paginate` or `--max-items`
- Single values: `--query 'Stacks[0].StackId' --output text`

## Common queries

```bash
aws ec2 describe-instances --profile epiccounty \
  --query 'Reservations[].Instances[].[InstanceId,InstanceType,State.Name,Tags[?Key==`Name`].Value|[0]]' --output table
aws s3 ls --profile epiccounty
aws cloudformation list-stacks --stack-status-filter CREATE_COMPLETE UPDATE_COMPLETE --profile epiccounty
aws logs tail <LOG_GROUP> --since 1h --profile epiccounty
aws ce get-cost-and-usage --time-period Start=2026-08-01,End=2026-08-20 --granularity DAILY \
  --metrics "UnblendedCost" --profile epiccounty
```

## Safety rules

Details in the **aws-security** skill — applies to every aws-* task. Core:

- Destructive commands (`terminate`, `delete`, `rm`, `rb`, IAM/SG changes) require:
  impact list first, dry-run, explicit approval, then execution
- Commands needing secret values: provide a placeholder template only; the user runs it
  in their own terminal (never the `!` prefix — it lands in the transcript)
- Never fetch plaintext from Secrets Manager / Parameter Store
- SSO over long-lived access keys. Scrub `AKIA...`, emails, pem from output (`<REDACTED>`)

## Session expiry

`Token has expired` / `ExpiredTokenException` → `aws sso login --profile epiccounty`, retry.
See the aws-access skill.

## Resource sweeps

All-region sweep: loop over `aws ec2 describe-regions --query 'Regions[].RegionName' --output text`.
For bulk output delegate to the aws-ops subagent — protects main context.
Prefer the unified primitives in aws-services over per-service describe loops.
