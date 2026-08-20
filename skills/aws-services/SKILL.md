---
name: aws-services
description: >-
  Observe and control nearly all AWS services — Resource Explorer/Tagging API unified
  queries plus per-service-group command map. Triggers: "all resources", "리소스 전체 조회",
  "aws services", "aws 서비스 목록", "ecs", "rds", "lambda", "cloudfront", "resource explorer".
---

# AWS Services Observation & Control Map

Control commands all go through the aws-security rules (list → approval → execute).
Every command uses `--profile epiccounty`. Detailed flags: model knowledge +
`aws <service> <op> help`.

## Unified observation — account-wide, service-agnostic

### Resource Explorer (one-time setup, searches all regions)

```bash
# One-time setup — create the index, wait for ACTIVE (minutes)
aws resource-explorer-2 create-index --region ap-northeast-2 --profile epiccounty
aws resource-explorer-2 get-index --region ap-northeast-2 --profile epiccounty

# Search
aws resource-explorer-2 search --query-string "*" --region ap-northeast-2 --profile epiccounty
# Filters: service:ec2 | resType:instance | tag.env:prod | region:us-east-1
```

One index per account. New resource types are picked up automatically.

### Tagging API (no setup, every tagged resource)

```bash
aws resourcegroupstaggingapi get-resources --profile epiccounty
# --tag-filters Key=env,Values=prod  --resource-type-filters ec2:instance s3
```

### Other unified views

| Tool | Purpose |
|------|---------|
| `ce get-cost-and-usage` (GROUP BY SERVICE) | spend per service |
| `configservice` (needs setup) | resource configuration and change history |
| `health describe-events` | account events (limited on personal accounts) |

## Per-service-group entry points

### Compute
| Service | Observe | Control |
|---------|---------|---------|
| ec2 | `describe-instances/volumes/images` | `start/stop-instances`, `create-image` |
| ecs | `list-clusters`, `describe-services` | `update-service --desired-count`, `run-task` |
| lambda | `list-functions`, `get-function` | `update-function-code`, `invoke` |
| batch | `list-job-queues` | `submit-job` |

### Storage / DB
| Service | Observe | Control |
|---------|---------|---------|
| s3 | `ls`, `api head-bucket`, `api list-objects-v2` | `cp/sync` (rm·rb under destructive rules) |
| ebs | `describe-volumes` | `create-volume` (delete is destructive) |
| rds | `describe-db-instances` | `start/stop-db-instance` (delete is destructive) |
| dynamodb | `list-tables`, `describe-table` | `update-table` |
| efs/fsx | `describe-file-systems` | — |

### Network
| Service | Observe | Control |
|---------|---------|---------|
| vpc | `describe-vpcs/subnets/route-tables/nat-gateways` | `create/delete-*` (delete is destructive) |
| elbv2 | `describe-load-balancers/target-groups` | `create-load-balancer`, `register-targets` |
| route53 | `list-hosted-zones`, `list-resource-record-sets` | `change-resource-record-sets` (snapshot first) |
| cloudfront | `list-distributions` | console/CDK preferred |
| apigateway | `get-rest-apis`, `get-stages` | — |

### Security / Identity
| Service | Observe | Control |
|---------|---------|---------|
| iam | `list-users/roles/policies`, `get-account-authorization-details` | all destructive — snapshot mandatory |
| kms | `list-keys`, `describe-key` | never automate key deletion |
| secretsmanager | `list-secrets`, `describe-secret` | **plaintext lookup forbidden** (aws-security) |
| ssm-param | `describe-parameters` | `--with-decryption` forbidden |
| acm | `list-certificates` | — |
| guardduty/securityhub | `list-detectors/get-findings` (if enabled) | — |

### Ops / Observability
| Service | Observe | Control |
|---------|---------|---------|
| cloudwatch | `get-metric-statistics`, `list-alarms` | `put-metric-alarm` |
| logs | `describe-log-groups`, `tail <grp>` | `create-log-group`, `delete-log-group` (destructive) |
| eventbridge | `list-rules` | `enable/disable-rule` |
| sns/sqs | `list-topics/queues` | `publish`, `send-message` |
| cloudformation | `list-stacks`, `describe-stack-events` | `deploy` (delete-stack is destructive) |
| backup | `list-backup-plans/vaults` | `start-backup-job` |

## Exploration order (delegate to aws-ops)

1. Unknown inventory → Resource Explorer `"*"` or the Tagging API
2. Specific service state → the group table's observe command
3. Cost-based review → `ce` GROUP BY SERVICE
4. Control → aws-security procedure (list → dry-run → approval → minimum scope → verify)

Bulk output (full inventory, multi-region) belongs in the aws-ops subagent —
protects main context.
