# Browser Automation — agent-browser × playwright 운영 규칙

이 프로젝트의 브라우저 자동화는 **agent-browser**(스킬→Rust CLI)와 **playwright**(MCP)를 통해 이뤄진다. 두 도구는 메커니즘(둘 다 accessibility-tree 스냅샷 + ref)은 같지만 강점 축이 다르므로, 다음 라우팅 규칙에 따라 분업한다.

## 1. 멘탈 모델

- **agent-browser = 넓은 일꾼**: Rust CLI(CDP). compact 출력으로 컨텍스트 효율이 높고, 세션·인증 vault·멀티계정이 영속하며, Electron/Slack/Sandbox/클라우드까지 폭넓게 커버한다.
- **playwright = 깊은 계측기**: MCP 네이티브. 임의 JS 평가·네트워크/콘솔 검사에 강하다.

| 차원 | agent-browser | playwright |
|---|---|---|
| 호출 방식 | Bash CLI (Rust, CDP) | MCP 도구 단위 (`browser_*`) |
| 컨텍스트 효율 | ★ 높음 (compact 출력) | 도구당 스냅샷 — 무거움 |
| 세션/인증 | 세션 영속, auth vault, 멀티계정 | 단일 세션 중심 |
| 임의 JS 실행 | 제한적 | ★ `browser_evaluate` |
| 네트워크/콘솔 | 기본 | ★ `network_requests`, `console_messages` |
| 특수 환경 | ★ Electron·Slack·Sandbox·클라우드 | 일반 웹 |

**한 줄 원칙**: 겹치면 agent-browser, 계측이 필요하면 playwright. 같은 작업에 두 도구를 동시에 쓰지 않는다.

## 2. 진입 경계 — 먼저 "브라우저가 필요한가"

| 필요 | 1순위 |
|---|---|
| 페이지 상호작용·JS 렌더링·인증·폼·클릭·스크린샷·E2E | 브라우저 (아래 §3) |
| 정적 공개 페이지 내용만 읽기 | `ctx_fetch_and_index` |
| 레포 내 파일 읽기 | Read / Grep |

## 3. 작업 유형별 1순위 도구 (라우팅 표)

| 작업 | 1순위 | 비고 |
|---|---|---|
| 탐색·폼 입력·클릭·스크린샷·스크래핑 | agent-browser | 겹침 구간 — 컨텍스트 효율 우선 |
| 탐색적 테스트·QA·dogfood·버그 헌트 | agent-browser | `agent-browser skills get dogfood` |
| 로그인/멀티계정/세션 영속·비디오 녹화 | agent-browser | auth vault |
| Electron 데스크톱앱(VS Code·Slack·Figma) | agent-browser | `agent-browser skills get electron` |
| Slack 워크스페이스 자동화 | agent-browser | `agent-browser skills get slack` |
| Vercel Sandbox / AWS Bedrock 클라우드 브라우저 | agent-browser | `skills get vercel-sandbox` / `agentcore` |
| 네트워크 요청 검사 | playwright | `browser_network_requests` |
| 콘솔 로그 추적 | playwright | `browser_console_messages` |
| 임의 JS 평가/주입 | playwright | `browser_evaluate` |

## 4. 워크플로 시퀀싱

### A. 웹앱 QA / dogfood
```
agent-browser skills get dogfood   # 탐색·스크린샷으로 이상 탐지
  → (이상 발견 시) playwright browser_network_requests / console_messages  # 심층 검사
```

### B. 폼 / 로그인 자동화
```
agent-browser 세션 시작   # auth vault로 인증 영속
  → snapshot 으로 @eN ref 확보 → fill / click → 상태 영속
```

## 5. 충돌·중복 방지 룰

1. 겹침 구간(일반 웹 작업)의 기본은 agent-browser — 컨텍스트 효율.
2. playwright는 네트워크 검사·콘솔 추적·임의 JS 평가 3개 구간에서만 1순위로 승격한다.
3. 한 작업을 두 브라우저 스택으로 혼용하지 않는다 — 별도 인스턴스라 상태가 공유되지 않는다.

## 6. 안티 패턴

- ❌ 일반 웹 작업을 playwright 도구 단위로 처리 — 컨텍스트 낭비.
- ❌ 정적 페이지 읽기에 브라우저 기동 — `ctx_fetch_and_index`면 충분.
- ❌ 같은 작업 도중 두 도구 혼용 — 상태 비공유.
