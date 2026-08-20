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
| `aws-ops` (agent) | Subagent for bulk AWS queries — returns summaries, never executes destructive commands |

## Install

```bash
# Claude Code
claude plugin marketplace add epiccounty/plugins
claude plugin install aws-toolkit@epiccounty

# Codex
codex plugin marketplace add epiccounty/plugins
codex plugin add aws-toolkit@epiccounty

# agy (repo URL, no .git)
agy plugin install https://github.com/epiccounty/aws-toolkit
agy plugin enable aws-toolkit

# hermes (repo URL)
hermes plugins install https://github.com/epiccounty/aws-toolkit
hermes plugins enable aws-toolkit
```

## License

MIT

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
| `aws-ops` (에이전트) | 대량 AWS 조회 전담 서브에이전트 — 요약 반환, 파괴적 명령 실행 금지 |

## 설치

위 영문 섹션의 Install 명령과 동일 (호스트별 명령은 코드 블록 참조).

## 라이선스

MIT
