# CLAUDE.md

## Behavioral Guidelines

작업 전 반드시 `.claude/rules/karpathy-guideline.md`를 따른다

## Script Command

<!-- TODO: 프로젝트 세팅 후 빌드·테스트·개발 서버 명령어를 채운다 -->

## Architecture

<!-- TODO: 프로젝트 소스 구조와 불변식(수정 금지 디렉터리 등)을 채운다 -->

## Key Info

<!-- TODO: 개발 서버 포트, Lint 명령 등 프로젝트 기본 정보를 채운다 -->

## Superpowers

Superpowers 산출물 경로 규칙: `.claude/rules/superpowers.md`

## Code Graph Tools (Serena × code-review-graph)

코드 탐색·리뷰·리팩터링은 두 MCP 도구(**Serena** = LSP 실시간 / **code-review-graph** = 영속 그래프)를 라우팅 규칙에 따라 분업한다. Grep/Read보다 두 도구를 우선 사용한다.

**한 줄 원칙**: CRG로 시작해 Serena로 끝낸다. 같은 질문에 두 도구를 동시에 부르지 않는다.

상세 라우팅·워크플로·메모리 관리 규칙: `.claude/rules/code-graph.md`

## Workflow & Documents

작업은 다음 단계를 따라 진행되며, 각 단계의 산출물은 지정된 경로에 누적된다.

| 단계 | Skill / Agent | 산출물 경로 |
|---|---|---|
| 0. Compound Read | `compound-solutions` (read) | (조회만, 파일 생성 없음) |
| 1. Interview | `deep-interview` | `docs/interviews/YYYY-MM-DD-<topic>.md` |
| 2. Brainstorming | `superpowers:brainstorming` | `docs/specs/YYYY-MM-DD-<topic>-design.md` |
| 3. Writing Plans | `superpowers:writing-plans` | `docs/plans/YYYY-MM-DD-<feature>.md` |
| 4. Review | `plan-*` / `code-*` review | (파일 누적 없음 — 단발성 리뷰) |
| 5. Compound | `compound-solutions` (write) | `docs/solutions/<category>/<file>.md` |

**단계 0 필수**: Brainstorming 시작 전 `compound-solutions` read 모드로 `docs/solutions/`의 선행 지식을 확인한다. 관련 문서가 없으면 결과 없음을 확인하고 바로 진행한다.

**IMPORTANT**: 각 단계가 마무리되면 **반드시** 다음 단계 또는 적절한 후속 skill을 사용자에게 제안한다. 예: interview 종료 → brainstorming 제안, plan 작성 완료 → execute 제안, 작업 종료 → compound write 제안.

상세 운영 규칙:
- Compound 자동 트리거 표: `.claude/rules/compound-cluster.md`
- Superpowers 산출물 경로 정의: `.claude/rules/superpowers.md`
