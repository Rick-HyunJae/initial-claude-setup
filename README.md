# initial-claude-setup

**Claude Code를 활용하는 기본 템플릿(plate)** 입니다. 새 프로젝트를 시작할 때 이 저장소를 복제하면
Claude Code의 규약·워크플로·스킬·에이전트가 미리 세팅된 상태로 출발할 수 있고,
**Codex(codex-cli)와의 연계**까지 동일한 단일 진실(single source of truth)을 공유하도록 설계되어 있습니다.

> 한 줄 요약: **CLAUDE를 쓰는 기본 plate + Codex 연계를 한 세트로 묶은 부트스트랩 템플릿.**

---

## 설계 방향

1. **CLAUDE 기본 템플릿화** — `CLAUDE.md`와 `.claude/rules/*`를 프로젝트 규약의 단일 진실로 두고,
   워크플로(인터뷰 → 브레인스토밍 → 플랜 → 리뷰 → 컴파운드)와 산출물 경로를 규격화한다.
2. **Codex 연계** — `AGENTS.md`를 Codex 진입점으로 두되, 규약을 *복사하지 않고 참조*만 하여 drift를 방지한다.
   Claude 전용 기능(Serena, Superpowers 스킬 등)과 Codex 공통 규약(산출물 경로, 행동 규칙)을 명확히 분리한다.
3. **plate화** — 위 두 축을 그대로 가져다 쓸 수 있도록 빈 디렉터리 구조와 규칙 문서를 미리 배치한다.

---

## 듀얼 진입점 구조

| 진입점 | 대상 | 역할 |
|---|---|---|
| `CLAUDE.md` | Claude Code | 프로젝트 규약의 **단일 진실**. 워크플로·산출물 경로·도구 라우팅 정의 |
| `AGENTS.md` | Codex (codex-cli) | Codex 진입점. CLAUDE 규약을 **참조만** 하고 Codex 고유 사항(도구명 매핑, MCP 1회 설정)만 보강 |

두 진입점은 같은 규칙 문서(`.claude/rules/*`)를 바라보므로, 규약을 한 곳에서 고치면 양쪽 도구에 동시에 반영됩니다.

---

## 디렉터리 구조

```
.
├── CLAUDE.md                 # Claude Code 규약 (단일 진실)
├── AGENTS.md                 # Codex 진입점 (참조 + Codex 전용 메모)
├── .mcp.json                 # 프로젝트 MCP 서버 (code-review-graph)
├── .claude/
│   ├── settings.json         # 공유 권한·훅 (PostToolUse/SessionStart)
│   ├── rules/                # 운영 규칙 (행동·코드그래프·superpowers·compound)
│   ├── skills/               # 리뷰·컴파운드·CRG·인터뷰 등 스킬
│   └── agents/               # 리뷰어·컴파운드 등 서브에이전트
└── docs/
    ├── interviews/           # 1. 인터뷰 산출물
    ├── specs/                # 2. 설계 산출물 (브레인스토밍)
    ├── plans/                # 3. 구현 플랜
    ├── solutions/            # 5. 컴파운드 지식
    └── development/          # FSD 아키텍처 등 참조 문서
```

> `.claude/settings.local.json`과 `.DS_Store`는 머신 종속·잡파일이므로 `.gitignore`로 제외됩니다.

---

## 워크플로 & 산출물 경로

작업은 단계별로 진행되며 각 단계의 산출물이 지정 경로에 누적됩니다. (상세: `CLAUDE.md`)

| 단계 | Skill / Agent | 산출물 경로 |
|---|---|---|
| 0. Compound Read | `compound-solutions` (read) | (조회만) |
| 1. Interview | `deep-interview` | `docs/interviews/YYYY-MM-DD-<topic>.md` |
| 2. Brainstorming | `superpowers:brainstorming` | `docs/specs/YYYY-MM-DD-<topic>-design.md` |
| 3. Writing Plans | `superpowers:writing-plans` | `docs/plans/YYYY-MM-DD-<feature>.md` |
| 4. Review | `plan-*` / `code-*` review | (단발성) |
| 5. Compound | `compound-solutions` (write) | `docs/solutions/<category>/<file>.md` |

---

## 코드 그래프 도구 (Serena × code-review-graph)

코드 탐색·리뷰·리팩터링은 두 MCP 도구를 라우팅 규칙에 따라 분업합니다. (상세: `.claude/rules/code-graph.md`)

- **Serena** = LSP 실시간 (정확한 심볼·참조·편집) — Claude 전용
- **code-review-graph (CRG)** = 영속 그래프 (영향 분석·리뷰·아키텍처) — Claude/Codex 공용
- **한 줄 원칙**: CRG로 시작해 Serena로 끝낸다.

---

## 시작하기

1. **이 저장소를 새 프로젝트의 출발점으로 복제**한다.
2. `CLAUDE.md`의 `## Script Command` / `## Architecture` / `## Key Info`를 프로젝트에 맞게 채운다.
3. **Claude Code**: `.mcp.json`의 code-review-graph가 자동 등록된다. Serena는 User Scope 1회 onboarding이 필요하다.
4. **Codex 연계**: code-review-graph MCP를 쓰려면 전역 `~/.codex/config.toml`에 1회 등록한다.

   ```toml
   # ~/.codex/config.toml
   [mcp_servers.code-review-graph]
   command = "uvx"
   args = ["code-review-graph", "serve"]
   ```

5. `AGENTS.md`의 `## 2. 절대 불변식` TODO(수정 금지 디렉터리·브랜치 정책)를 프로젝트에 맞게 채운다.

---

## 핵심 규칙 문서

| 문서 | 내용 |
|---|---|
| `.claude/rules/karpathy-guideline.md` | 행동 규칙 (도구 무관, 양쪽 공통) |
| `.claude/rules/code-graph.md` | Serena × CRG 라우팅·워크플로 |
| `.claude/rules/superpowers.md` | Superpowers 산출물 경로·격리 규약 (Claude 전용) |
| `.claude/rules/compound-cluster.md` | Compound 자동 트리거 규약 |
