# aws-toolkit

> Multi-host plugin (Claude Code · Codex · agy · hermes) — integrated AWS access:
> SSO login, SSM instance sessions and tunnels, general AWS CLI operations, security
> guardrails, onboarding, and an account-wide service map. Built for the epiccounty
> AWS account (Identity Center / SSO, no long-lived keys).

## Skills & Agent

| Component | Purpose |
|-----------|---------|
| `aws-access` | SSO login, profiles, session expiry |
| `aws-ssm` | Instance access, port-forward tunnels, commands, file transfer |
| `aws-cli` | CLI patterns, output control, cost checks, safety summary |
| `aws-onboarding` | Setup path for new users/machines (tools → config → first session) |
| `aws-security` | PII handling, secret-entry protocol, destructive command control |
| `aws-services` | Account-wide observation (Resource Explorer, Tagging API) + per-service-group command map |
| `aws-ops` (agent) | Subagent for bulk AWS queries — report-only by default; executes destructive commands only after explicit per-resource approval |

## Requirements

- AWS CLI v2 with an Identity Center (SSO) profile — the skills default to
  `--profile epiccounty`
- `session-manager-plugin` (SSM sessions and tunnels)
- macOS is assumed by the onboarding/skill examples (`brew`, `~/.aws/config`)
- A host CLI: Claude Code, Codex, agy, or hermes

## Install

```bash
# Claude Code (marketplace manifest is self-hosted in this repo)
claude plugin marketplace add epiccounty/aws-toolkit
claude plugin install aws-toolkit@epiccounty

# Codex (catalog manifest is self-hosted in this repo)
codex plugin marketplace add epiccounty/aws-toolkit
codex plugin add aws-toolkit@aws-toolkit

# agy (repo URL, no .git)
agy plugin install https://github.com/epiccounty/aws-toolkit
agy plugin enable aws-toolkit

# hermes (repo URL)
hermes plugins install https://github.com/epiccounty/aws-toolkit
hermes plugins enable aws-toolkit
```

> **hermes note**: hermes' install scanner may flag this repo's `AGENTS.md` as
> persistence and block the install. If it does, set `plugins.scan_on_install:
> false` in your hermes config, or install with the force/confirm path your
> version offers, then re-enable scanning.

## Quick start

```bash
aws sso login --profile epiccounty   # refresh the session first
```

Then just ask in your agent session:

- "aws sso login" — session status and login flow
- "인스턴스 상태" / "instance status" — bulk instance queries via the `aws-ops` agent
- "ssm tunnel to i-xxxx 5432" — port-forward into an RDS-style target

## Updating

```bash
# Claude Code
claude plugin marketplace update epiccounty && claude plugin install aws-toolkit@epiccounty

# Codex
codex plugin marketplace upgrade aws-toolkit

# agy — reinstall over the existing copy
agy plugin install https://github.com/epiccounty/aws-toolkit

# hermes
hermes plugins update aws-toolkit
```

## Uninstall

```bash
claude plugin uninstall aws-toolkit@epiccounty
codex plugin remove aws-toolkit@aws-toolkit
agy plugin disable aws-toolkit
hermes plugins disable aws-toolkit && hermes plugins uninstall aws-toolkit
```

## License

Apache-2.0 — see [LICENSE](LICENSE).

---

# aws-toolkit (한국어)

> 멀티호스트 플러그인 (Claude Code · Codex · agy · hermes) — AWS 통합 접근:
> SSO 로그인, SSM 인스턴스 접속·터널, 범용 AWS CLI 사용, 보안 가드레일, 온보딩,
> 계정 전체 서비스 맵. epiccounty 계정 기준 (Identity Center / SSO, 장기 키 없음).

## 스킬 & 에이전트

| 구성 | 용도 |
|------|------|
| `aws-access` | SSO 로그인, 프로필, 세션 만료 처리 |
| `aws-ssm` | 인스턴스 접속, 포트 포워딩 터널, 명령 실행, 파일 전송 |
| `aws-cli` | CLI 패턴, 출력 제어, 비용 조회, 안전 규칙 요약 |
| `aws-onboarding` | 신규 유저/신규 머신 설정 절차 (도구 → config → 첫 접속) |
| `aws-security` | PII 처리, 시크릿 입력 프로토콜, 파괴적 명령 제어 |
| `aws-services` | 계정 전체 관측 (Resource Explorer, Tagging API) + 서비스 그룹별 명령 맵 |
| `aws-ops` (에이전트) | 대량 AWS 조회 전담 서브에이전트 — 기본은 조회·보고 전용이며, 명시적 리소스 단위 승인 후에만 파괴적 명령 실행 |

## 요구 사항

- Identity Center(SSO) 프로필이 설정된 AWS CLI v2 (기본 프로필 `epiccounty`)
- `session-manager-plugin` (SSM 세션/터널)
- 온보딩/스킬 예시는 macOS(`brew`, `~/.aws/config`) 기준
- 호스트 CLI: Claude Code, Codex, agy, hermes 중 하나

## 설치

```bash
# Claude Code (마켓플레이스 매니페스트를 이 저장소가 직접 제공)
claude plugin marketplace add epiccounty/aws-toolkit
claude plugin install aws-toolkit@epiccounty

# Codex (카탈로그 매니페스트를 이 저장소가 직접 제공)
codex plugin marketplace add epiccounty/aws-toolkit
codex plugin add aws-toolkit@aws-toolkit

# agy
agy plugin install https://github.com/epiccounty/aws-toolkit
agy plugin enable aws-toolkit

# hermes
hermes plugins install https://github.com/epiccounty/aws-toolkit
hermes plugins enable aws-toolkit
```

> **hermes 주의**: 설치 스캐너가 이 저장소의 `AGENTS.md`를 persistence 로 판정해
> 설치를 막을 수 있습니다. 그 경우 hermes 설정에 `plugins.scan_on_install: false`를
> 넣거나, 사용 중인 버전의 force/confirm 경로로 설치한 뒤 스캔을 다시 켜세요.

## 빠른 시작

```bash
aws sso login --profile epiccounty   # 먼저 세션 갱신
```

에이전트 세션에서 바로:

- "aws sso login" — 세션 상태 확인과 로그인
- "인스턴스 상태" — `aws-ops` 에이전트로 대량 조회
- "ssm tunnel to i-xxxx 5432" — 포트 포워딩 터널

## 업데이트

```bash
claude plugin marketplace update epiccounty && claude plugin install aws-toolkit@epiccounty
codex plugin marketplace upgrade aws-toolkit
agy plugin install https://github.com/epiccounty/aws-toolkit
hermes plugins update aws-toolkit
```

## 제거

```bash
claude plugin uninstall aws-toolkit@epiccounty
codex plugin remove aws-toolkit@aws-toolkit
agy plugin disable aws-toolkit
hermes plugins disable aws-toolkit && hermes plugins uninstall aws-toolkit
```

## 라이선스

Apache-2.0 — [LICENSE](LICENSE) 참고.
