# SW Design Workflow

## 프로젝트 목적
시스템의 SW 설계 산출물을 10개의 에이전트가 **순차적으로** 생성하는 파이프라인이다.
각 에이전트는 직전 단계의 산출물을 주 입력으로 받아 다음 단계의 산출물을 만든다. 추적성·검증을 위해 필요한 경우 이전 단계들의 산출물도 함께 참조한다.

## 파이프라인
```
SRS.md
  → [1. SRS]          → docs/output/srs
  → [2. Features]     → docs/output/features
  → [3. Wireframe]    → docs/output/wireframe
  → [4. Prototype]    → docs/output/prototype
  → [5. UI]           → docs/output/ui
  → [6. Data]         → docs/output/data
  → [7. Architecture] → docs/output/architecture
  → [8. Security]     → docs/output/security
  → [9. Test]         → docs/output/test
  → [10. DevOps]      → docs/output/devops
```

## 에이전트 구성

| 단계 | 에이전트 | 룰 파일 | 주 입력 | 참조 입력 | 출력 |
|---|---|---|---|---|---|
| 1 | SRS | [.claude/rules/agent-srs.md](.claude/rules/agent-srs.md) | `SRS.md` | — | `docs/output/srs/` |
| 2 | Features | [.claude/rules/agent-features.md](.claude/rules/agent-features.md) | `docs/output/srs/` | — | `docs/output/features/` |
| 3 | Wireframe | [.claude/rules/agent-wireframe.md](.claude/rules/agent-wireframe.md) | `docs/output/features/` | — | `docs/output/wireframe/` |
| 4 | Prototype | [.claude/rules/agent-prototype.md](.claude/rules/agent-prototype.md) | `docs/output/wireframe/` | `docs/output/features/` | `docs/output/prototype/` |
| 5 | UI | [.claude/rules/agent-ui.md](.claude/rules/agent-ui.md) | `docs/output/prototype/` | `docs/output/wireframe/`, `docs/output/features/` | `docs/output/ui/` |
| 6 | Data | [.claude/rules/agent-data.md](.claude/rules/agent-data.md) | `docs/output/ui/` | `docs/output/wireframe/`, `docs/output/features/`, `docs/output/prototype/` | `docs/output/data/` |
| 7 | Architecture | [.claude/rules/agent-architecture.md](.claude/rules/agent-architecture.md) | `docs/output/data/` | `docs/output/features/` (NFR) | `docs/output/architecture/` |
| 8 | Security | [.claude/rules/agent-security.md](.claude/rules/agent-security.md) | `docs/output/architecture/` | `docs/output/data/`, `docs/output/features/` (NFR) | `docs/output/security/` |
| 9 | Test | [.claude/rules/agent-test.md](.claude/rules/agent-test.md) | `docs/output/security/` | `docs/output/srs/`, `docs/output/features/`, `docs/output/architecture/` | `docs/output/test/` |
| 10 | DevOps | [.claude/rules/agent-devops.md](.claude/rules/agent-devops.md) | `docs/output/test/` | `docs/output/architecture/`, `docs/output/security/` | `docs/output/devops/` |

## 에이전트 호출 방법
사용자는 자연어로 단계를 지정한다. 예시:
- **1. SRS**: "SRS 단계 실행해줘" / "요구사항 분석해줘"
- **2. Features**: "Features 산출물 만들어줘" / "기능 명세 작성해줘"
- **3. Wireframe**: "Wireframe 그려줘" / "화면 설계해줘"
- **4. Prototype**: "Prototype 만들어줘" / "클릭 가능한 프로토타입 만들어줘"
- **5. UI**: "UI 디자인 만들어줘" / "화면 디자인 완성해줘"
- **6. Data**: "데이터 설계해줘" / "API·스키마 설계해줘"
- **7. Architecture**: "아키텍처 설계해줘" / "시스템 구성도 만들어줘"
- **8. Security**: "보안 설계해줘" / "위협 모델링 해줘"
- **9. Test**: "테스트 설계해줘" / "테스트 케이스 만들어줘"
- **10. DevOps**: "DevOps 산출물 만들어줘" / "CI/CD 파이프라인 설계해줘"

Claude는 키워드에 해당하는 룰 파일을 읽고 그 **역할**로 진입한 뒤, 룰 파일의 **수행 작업**을 순서대로 실행한다. 단계 간 의존성을 위해 표의 **참조 입력**도 함께 읽는다.

"전체 파이프라인 실행" 또는 "처음부터 끝까지" 요청 시 1→10을 순차 실행한다. 단계 사이에 결과를 사용자에게 보고하고 다음 단계 진행 여부를 확인한다.

## 재실행/복구 절차
특정 단계가 실패했거나 산출물을 다시 만들어야 할 때:
1. 해당 단계의 **출력 디렉터리만** 정리한다 (이전 단계 산출물은 보존).
2. 해당 단계를 재실행한다.
3. 입력이 바뀌었으므로 **하위 단계들도 재생성**이 필요한지 사용자에게 확인한다 (단계 N 변경 시 N+1 ~ 10 영향 가능).

이전 단계의 산출물이 없는 상태로 다음 단계 실행을 요청받으면, 작업을 시작하지 말고 사용자에게 알린 뒤 누락된 가장 빠른 단계부터 거슬러 올라가 실행한다.
