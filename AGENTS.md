# AGENTS.md — Codex 진입점

이 파일은 Codex(codex-cli)용 진입점이다. **단일 진실은 `CLAUDE.md`와 `.claude/rules/*`**이며,
여기서는 그 내용을 *복사하지 않고 참조*만 한다(drift 방지). Codex 고유 사항만 아래에 보강한다.

## 1. 먼저 읽을 것 (참조)

- `CLAUDE.md` — 프로젝트 규약 전반. 단, "Codex 비적용" 항목은 무시한다.
  - ✅ 적용: `## Script Command`(빌드·테스트 명령), `## Architecture`(구조 + 불변식), `## Key Info`(포트·Lint)
  - ✅ 적용: `## Workflow & Documents`의 **산출물 경로 규약**(`docs/specs/`, `docs/plans/`, `docs/solutions/`)
  - ❌ 비적용: `## Superpowers`, `## Code Graph Tools`의 Serena 부분 — Claude 전용(아래 3절 참고)
- `.claude/rules/karpathy-guideline.md` — 행동 규칙(도구 무관). 반드시 따른다.

## 2. 절대 불변식 (안전 가드 — 직접 명시)

<!-- TODO: 프로젝트 세팅 후 수정 금지 디렉터리·브랜치 정책을 채운다 -->
- **long-running 통합 브랜치(`main`, `master`)에 직접 코드 커밋 금지.** 전용 브랜치에서 작업한다.

## 3. Codex 전용 메모

### 도구명 매핑 (규칙 문서가 Claude 도구명으로 쓰인 경우)

| Claude 도구명 | Codex에서 의미 |
|---|---|
| Read / Grep / Glob | 파일 읽기·검색 |
| Edit / Write | 파일 수정·생성 |
| Skill / Agent / Task | 해당 개념 없음 — 무시 |

### Claude 전용 → Codex에 없는 것 (참고만 가능, 자동 동작 없음)

- `.claude/skills/*`, `.claude/agents/*` — 자동 트리거 안 됨. 필요하면 해당 `.md`를 직접 읽어 *내용만* 참고한다.
- **Serena MCP**(User Scope, Claude 전용), `PostToolUse`/`SessionStart` 훅 — Codex는 code-review-graph 그래프를 자동 갱신하지 않는다.
- `.claude/rules/superpowers.md`·`compound-cluster.md` — Claude 스킬 파이프라인 전제. Codex는 산출물 경로 규약만 따른다.

### MCP(code-review-graph) 1회 설정 — git 공유 불가

Codex는 프로젝트 `.codex/config.toml`을 읽지 않고 **전역 `~/.codex/config.toml`만 로드**한다.
code-review-graph MCP를 쓰려면 각 개발자가 1회 등록한다:

```toml
# ~/.codex/config.toml
[mcp_servers.code-review-graph]
command = "uvx"
args = ["code-review-graph", "serve"]
```

> 그래프는 프로젝트 루트에서 실행 시 `./.code-review-graph/` 를 사용한다(cwd 미지정 시 자동).
