# Solutions Knowledge Base

이 디렉토리는 DOUZONE Data Visualization Editor (TypeScript/React/ProseMirror/TipTap) 프로젝트에서 해결한 문제와 확립된 패턴을 문서화합니다.

문서는 `ce-compound` 스킬이 자동으로 작성하며, `compound-solutions` 에이전트를 통해 관리됩니다.

## 디렉토리 구조

각 서브디렉토리는 `schema.yaml`의 `problem_type`에 대응합니다:

| 디렉토리 | problem_type | 트랙 |
|---|---|---|
| `build-errors/` | build_error | Bug |
| `test-failures/` | test_failure | Bug |
| `runtime-errors/` | runtime_error | Bug |
| `performance-issues/` | performance_issue | Bug |
| `security-issues/` | security_issue | Bug |
| `ui-bugs/` | ui_bug | Bug |
| `integration-issues/` | integration_issue | Bug |
| `logic-errors/` | logic_error | Bug |
| `best-practices/` | best_practice | Knowledge |
| `architecture-patterns/` | architecture_pattern | Knowledge |
| `design-patterns/` | design_pattern | Knowledge |
| `conventions/` | convention | Knowledge |
| `developer-experience/` | developer_experience | Knowledge |
| `workflow-issues/` | workflow_issue | Knowledge |
| `tooling-decisions/` | tooling_decision | Knowledge |
| `documentation-gaps/` | documentation_gap | Knowledge |

## 사용법

- 문제 해결 후 → `compound-solutions` 에이전트 (write 모드) 또는 `/ce-compound`
- 새 작업 착수 전 → `compound-solutions` 에이전트 (read 모드)
- 정기 정비 → `/ce-compound-refresh`

## Schema

frontmatter 계약은 `.claude/skills/ce-compound/references/schema.yaml` 참조.
