---
name: aws-ssm
description: >-
  SSM Session Manager instance access, port-forwarding tunnels, remote command
  execution, and file transfer. Triggers: "ssm", "instance access", "인스턴스 접속",
  "tunnel", "터널", "port forwarding", "포트 포워딩", "rds 접속", "session manager".
---

# SSM Instance Access (no SSH)

Prerequisites:
- `session-manager-plugin` installed — check: `session-manager-plugin --version`,
  else `brew install --cask session-manager-plugin`
- Instance has `ssm-instance-role` attached
- Instance allows outbound 443

## List instances / open a shell

```bash
aws ssm describe-instance-information --profile epiccounty \
  --query 'InstanceInformationList[].[InstanceId,ComputerName,PingStatus,PlatformName]' --output table

aws ssm start-session --target <INSTANCE_ID> --profile epiccounty
```

## Port-forwarding tunnels

```bash
# Instance-local port -> local (e.g. instance 80 -> local 8080)
aws ssm start-session --target <INSTANCE_ID> \
  --document-name AWS-StartPortForwardingSession \
  --parameters '{"portNumber":["80"],"localPortNumber":["8080"]}' \
  --profile epiccounty

# Remote host -> local (private RDS/Redis via instance as relay)
aws ssm start-session --target <INSTANCE_ID> \
  --document-name AWS-StartPortForwardingSessionToRemoteHost \
  --parameters '{"host":["<RDS_ENDPOINT>"],"portNumber":["3306"],"localPortNumber":["13306"]}' \
  --profile epiccounty
```

Tunnels block in the foreground — use a separate terminal or `nohup` for background.

## Run commands without a session

```bash
aws ssm send-command --instance-ids <INSTANCE_ID> \
  --document-name AWS-RunShellScript \
  --parameters 'commands=["uptime","df -h"]' --profile epiccounty

aws ssm list-command-invocations --command-id <COMMAND_ID> --details --profile epiccounty
```

## File transfer

No scp over SSM. Route via S3:
```bash
aws s3 cp <local-file> s3://<bucket>/staging/ --profile epiccounty
aws ssm send-command ... --parameters 'commands=["aws s3 cp s3://<bucket>/staging/<file> /tmp/"]'
```
Instance-side S3 access requires an extra policy on `ssm-instance-role`.

## Launching instances (SSM-only pattern)

```bash
aws ec2 run-instances \
  --image-id resolve:ssm:/aws/service/ami-amazon-linux-latest/al2023-ami-kernel-default-x86_64 \
  --instance-type t3.micro \
  --iam-instance-profile Name=ssm-instance-role \
  --security-group-ids <SG_WITHOUT_PORT_22> \
  --profile epiccounty
```

Rules: no port 22 inbound, no key pair needed, access only via `start-session`.

## Troubleshooting

| Symptom | Cause / fix |
|---------|-------------|
| Empty list / `TargetNotConnected` | Role not attached, outbound 443 blocked, agent down (`systemctl status amazon-ssm-agent`) |
| New instance missing | Registration takes a few minutes — wait |
| `session-manager-plugin not found` | Reopen terminal after brew install |
