# SW 설계 프로세스 (Agentic AI 기반 완전 자동화 설계 방법론)

에이전트 AI 기반 SW 설계의 핵심 철학은 **"사람의 개입 수준 0%"** 를 지향합니다.
모든 설계 단계를 AI(Every Assistant / Every Agent AI, Claude Design 등)가 주도하며, 최종 산출물만으로 실제 구현했을 때 **요구사항명세서(SRS) 충족률 100% (미충족률 0%)** 를 달성하는 것을 최종 목표로 합니다.

다만 완전 자동화를 실무적으로 달성하기 위해서는 단순히 산출물을 생성하는 것만으로는 부족합니다. 각 단계의 **입력물과 출력물, 요구사항 추적성, 품질 게이트, 예외 흐름, 테스트 설계, AI 산출물 검증, 운영 설계**까지 함께 정의해야 합니다.

> **자동화 성숙도(Level 0~5) 정의**
> - Level 0: 모든 단계 사람 주도, AI는 보조
> - Level 1: AI가 1차 산출물 생성, 사람이 전면 검토/수정
> - Level 2: AI가 산출물 생성, 사람은 핵심 의사결정 지점만 승인
> - Level 3: AI가 산출물 생성·검증, 사람은 예외 상황만 개입
> - Level 4: AI가 단계 간 피드백 루프까지 자동 처리, 사람은 최종 릴리즈만 승인
> - Level 5: 완전 자율 (사람 개입 0%)
>
> 본 문서는 **Level 4 도달**을 단기 목표, **Level 5**를 최종 목표로 설정합니다.

---

## 1. 전체 설계 프로세스 요약 (단계별 개요)

| 순서 | 설계 단계 | 활용 AI / 도구 | 입력물 | 주요 산출물 | 검증 기준 | 예상 소요 | 토큰/비용 |
|------|----------|----------------|--------|-------------|-----------|-----------|-----------|
| 0 | **요구사항 수집 / SRS 작성** | Interview Agent + SRS Generator | 비즈니스 목표, 이해관계자 인터뷰, 도메인/경쟁 제품 분석 | SRS 문서 (IEEE 830 / ISO/IEC/IEEE 29148) | 모호성·중복·충돌 0건 | 1~3일 | 중 |
| 1 | **기능 설계** | Every Assistant / Agent AI | SRS, 비즈니스 목표, 사용자/권한 정보 | 기능명세서, 기능 트리, 유스케이스, 권한 매트릭스, NFR 목록 | SRS 100% 매핑 | 1~2일 | 중 |
| 2 | **와이어프레임 설계** | Figma + Claude Design | 기능명세서, 유스케이스, 사용자 흐름 | 화면 목록, 화면별 기능 배치도, 사용자 흐름, 화면-기능 매핑표 | 기능 ↔ 화면 1:1 매핑 | 1일 | 저 |
| 3 | **프로토타입 구현** | Every Agent AI, Figma | 와이어프레임, 화면 흐름, 핵심 시나리오 | Clickable Prototype, AI 페르소나 시뮬레이션 로그, 이슈/개선 목록 | 정상·예외 흐름 모두 검증 | 1~2일 | 중 |
| 4 | **사용자 인터페이스 설계** | Figma + Claude Design | 와이어프레임, 프로토타입 피드백, 브랜드/접근성 기준 | 화면설계서, 컴포넌트 명세, 상태별 화면, 반응형 레이아웃 | 와이어프레임 + NFR + 접근성 반영 | 2~3일 | 중 |
| 5 | **데이터 설계** | Every Assistant / Agent AI | 화면설계서, 사용자 액션 목록, 권한 정책 | API 명세서(OpenAPI), 테이블 명세서, ERD, 데이터 흐름도, 권한별 접근 정책 | 화면 액션 ↔ API/DB 누락 0건 | 1~2일 | 중 |
| 6 | **시스템 아키텍처 설계** | Every Assistant / Agent AI | 기능명세서, NFR, 데이터 설계, 트래픽/운영 조건 | 시스템 구성도, 기술 스택 결정서, 캐싱·MQ·관측 전략 | 성능·보안·확장성·비용 근거 포함 | 1~2일 | 중 |
| 7 | **보안 설계** | Security Agent | 기능명세서, 데이터 설계, 아키텍처 | 위협 모델(STRIDE), 인증/인가 설계, 암호화/키 관리 정책, 비밀정보 관리 전략 | OWASP Top 10 + ASVS 기반 검증 | 1일 | 중 |
| 8 | **테스트 설계** | QA Agent | SRS, 기능명세서, 화면설계서, API 명세서 | 인수 기준, E2E/API/단위 테스트, 권한·보안·접근성·성능 시나리오 | SRS 항목별 검증 방법 100% 매핑 | 1~2일 | 중 |
| 9 | **배포 / 운영(DevOps) 설계** | DevOps / SRE Agent | 아키텍처, 보안 요구사항, 운영 정책 | CI/CD, 환경 구성, 배포 전략, 롤백, 모니터링/알림, 백업/복구, 장애 Runbook, SLO/SLI | 무중단 배포 + 자동 장애 탐지/대응 | 1일 | 중 |

---

## 2. 각 단계 상세 설명 및 보완 포인트

### 0. 요구사항 수집 / SRS 작성 (Requirements Elicitation)
- **목적**: 이해관계자 인터뷰, 도메인 분석, 경쟁 제품 분석을 통해 SRS를 자동 생성
- **AI 활용 방법**:
  - **Interview Agent**: 의사결정 트리 기반 동적 질문 생성 — 이전 답변에 따라 후속 질문을 분기 (예: "B2C SaaS" 답변 시 사용자 페르소나 5종 자동 도출 후 각각 인터뷰)
  - **Stakeholder Persona Agent**: CEO·도메인 전문가·최종 사용자·규제 담당자 역할을 시뮬레이션해 단일 인터뷰에서 빠지는 관점을 보완
  - **Domain Research Agent**: 웹 검색 + 경쟁 제품 분석(RAG)으로 업계 표준·규제·관행을 자동 반영
  - **SRS Generator**: IEEE 830 / ISO/IEC/IEEE 29148 템플릿 + Few-shot 예시로 표준 형식 변환. 각 항목에 ID, 우선순위(MoSCoW), 검증 기준, 출처를 자동 부여
  - **Conflict Detection Agent**: LLM-as-Judge로 항목 간 의미 충돌·중복·모호성 판정 (Self-Consistency: 동일 항목 3회 평가 후 다수결)
  - **프롬프트 전략**: Chain-of-Verification(생성 → 검증 질문 자체 생성 → 자체 답변)으로 환각 최소화
- **검증 기준**: SRS 항목 간 모호성·중복·충돌 0건 (Conflict Detection Agent 자동 검수)
- **입출력 포맷**: Markdown + 구조화된 YAML(요구사항 ID, 우선순위, 검증 기준 포함)

### 1. 기능 설계 (Functional Design)
- **목적**: SRS를 바탕으로 SW가 반드시 포함해야 하는 모든 기능 및 비기능 요구사항을 상세하고 명확하게 명세
- **중요 원칙**: "어떻게 구현할 것인가"가 아닌 **"무엇을 해야 하는가"** 에 집중
- **주요 산출물**: 기능명세서, 기능 트리, 유스케이스 목록, 사용자 역할/권한 매트릭스, NFR 목록
- **검증 기준**:
  - 모든 SRS 항목이 최소 1개 이상의 기능 또는 NFR로 연결
  - **각 기능에는 정상 흐름, 예외 흐름, 권한 조건, 완료 조건이 명시**
  - **모호한 표현(빠르게/편리하게/안정적으로 등)은 측정 가능한 기준으로 변환**
- **AI 활용 방법**:
  - **Functional Decomposer Agent**: SRS → 기능 트리(루트 → 모듈 → 기능 → 서브기능) 분해. Tree-of-Thoughts로 분기 평가하여 분해 깊이 일관성 확보
  - **Use Case Generator**: 각 기능에 액터·전제조건·주흐름·대안흐름·예외흐름·완료조건을 자동 부여
  - **NFR Extractor**: SRS 본문에 흩어진 비기능 요구를 추출 → 8개 NFR 카테고리(성능/보안/접근성/확장성/운영성/개인정보/국제화/비용)에 분류
  - **Permission Matrix Agent**: 역할 ↔ 기능 매트릭스 자동 생성 + 누락 권한 자동 검출
  - **Ambiguity Detector**: "빠르게/편리하게/안정적으로" 등 측정 불가 표현을 탐지해 측정 가능한 기준(p95 응답시간, 변환율 등)으로 재작성 제안
  - **프롬프트 전략**: Few-shot with structured templates + ReAct(추론 → 검색 → 정제), JSON Schema Constrained Decoding으로 형식 일탈 차단
- **입출력 포맷**: SRS 요구사항 ID와 1:N 매핑되는 구조화된 JSON/YAML

### 2. 와이어프레임 설계 (Wireframe Design)
- **목적**: 기능명세서에 정의된 기능을 화면에 어떻게 배치할지 결정
- **주요 산출물**: 화면 목록, 화면별 기능 배치도, 주요 사용자 흐름, 화면-기능 매핑표
- **검증 기준**:
  - 기능명세서의 모든 사용자 기능이 최소 1개 이상의 화면에 매핑
  - 화면 간 이동 경로가 끊기지 않음
  - **핵심 사용자 여정이 3~5개 수준으로 명확히 정의**
  - **권한별 접근 가능 화면이 구분**
- **AI 활용 방법**:
  - **Information Architect Agent**: 기능 트리 → 화면 그룹화 + 내비게이션 구조(IA) 자동 도출
  - **Wireframe Generator**: Figma Plugin API / Figma Make MCP 호출로 저해상도 와이어프레임 일괄 생성
  - **User Flow Mapper**: 핵심 시나리오 → 화면 전이 다이어그램(Mermaid/Figma) 자동 생성
  - **Coverage Checker Agent**: 기능 ID ↔ 화면 ID 매핑 자동 검증, 미매핑 항목을 이슈 로그로 기록
  - **Vision Agent (멀티모달)**: 생성된 와이어프레임을 캡처 분석하여 정보 밀도·여백·내비게이션 일관성 점검
  - **프롬프트 전략**: 산출물 스키마(JSON)를 시스템 프롬프트에 명시 + Constrained Decoding으로 형식 일탈 차단
- **입출력 포맷**: Figma 파일 링크 + 기능-화면 매핑 테이블(JSON)

### 3. 프로토타입 구현 (Prototype Implementation)
- **목적**: 와이어프레임을 클릭 가능한 MVP로 구현하여 사용자 흐름의 타당성을 조기 검증
- **주요 산출물**: Clickable Prototype, AI 페르소나 시뮬레이션 로그, 발견 이슈 목록, 개선 요청 목록
- **AI 활용 방법**:
  - **Prototype Builder**: Figma Make/Plugin API로 와이어프레임 → 클릭 가능한 프로토타입 자동 생성
  - **Persona Agent (5종 이상)**: 신규/숙련/고령/장애 보조기술 사용자/저사양 디바이스 페르소나로 동일 시나리오를 병렬 실행
  - **Scenario Runner**: Gherkin 시나리오를 BDD 형식으로 자동 실행, 각 단계의 성공/실패·소요 시간·클릭 경로를 로그로 기록
  - **UX Issue Detector**: 이탈 지점·반복 클릭·내비게이션 혼동 패턴을 자동 탐지하여 개선 요청 도출
  - **Vision Agent (멀티모달)**: 화면 캡처를 분석해 시각적 계층 구조·정보 밀도·CTA 가시성 문제 진단
  - **프롬프트 전략**: 페르소나별로 system prompt를 분리하고, 각 페르소나에 행동 지침·인지 한계·우선순위를 명시
- **검증 방식**: **AI 페르소나 시뮬레이션** (다양한 사용자 유형을 모사한 Persona Agent들이 자동으로 시나리오 실행) → 사람 개입 0% 원칙 유지
- **검증 기준**:
  - 정상 흐름뿐 아니라 **실패, 취소, 권한 없음, 데이터 없음, 입력 오류, 네트워크 오류** 등 예외 흐름 포함
  - 주요 사용자 시나리오가 프로토타입에서 끝까지 수행 가능
  - 각 시나리오의 성공/실패 조건이 로그로 기록 → 다음 단계 입력으로 활용

### 4. 사용자 인터페이스 설계 (UI Design)
- **목적**: 사용자가 직접 경험하는 최종 외형과 상호작용을 완성
- **중점**: 사용성, 접근성, 브랜딩, 컴포넌트 일관성, 화면 상태 정의
- **횡단 관심사**:
  - **접근성(a11y)**: WCAG 2.2 AA 등급 준수 자동 검증 (키보드 조작, 명도 대비, 대체 텍스트 등)
  - **국제화(i18n)**: 다국어/RTL/통화/날짜 형식 처리
  - **디자인 시스템**: 컴포넌트 라이브러리 표준화 (Material, Ant, 자체 시스템 등)
- **AI 활용 방법**:
  - **Visual Designer (Claude Design / Figma AI)**: 와이어프레임 + 디자인 시스템 토큰을 입력으로 고해상도 UI 자동 생성
  - **Component Library Agent**: 사전 정의된 컴포넌트(Material/Ant/자체)에 매핑하여 일관성 강제, 신규 컴포넌트는 디자인 시스템 등록 워크플로로 자동 라우팅
  - **State Coverage Agent**: 모든 화면에 5가지 상태(loading/empty/error/disabled/success) 자동 생성 — 누락 시 자동 보완
  - **a11y Auditor**: 명도 대비, 포커스 순서, ARIA 속성, 키보드 조작을 WCAG 2.2 AA 기준으로 자동 검사
  - **i18n Extractor**: 모든 사용자 표시 텍스트를 리소스 키로 추출 + RTL/통화/날짜/숫자 포맷 자동 매핑
  - **Microcopy Generator**: 입력 검증 오류 메시지·빈 상태 안내 문구를 톤·언어별로 일관되게 자동 생성
  - **Responsive Layout Agent**: 브레이크포인트별(mobile/tablet/desktop/wide) 레이아웃 자동 변형 + 가독성/터치 타겟 검증
- **주요 산출물**: 화면설계서, UI 컴포넌트 명세, 상태별 화면, 반응형 레이아웃 기준
- **검증 기준**:
  - **모든 화면에 필요한 상태별 UI(loading, empty, error, disabled, success) 정의**
  - **주요 입력 요소에 검증 규칙과 오류 메시지가 명시**
  - 접근성 기준 포함

### 5. 데이터 설계 (Data Design)
- **순서 중요**: 반드시 UI 설계 완료 후 진행
- **목적**: 사용자가 화면에서 생성·조회·수정·삭제하는 모든 데이터를 예측하고, 효율적인 스키마와 API 설계
- **주요 산출물**: API 명세서(OpenAPI 3.x), 테이블 명세서, ERD, 데이터 흐름도, 권한별 데이터 접근 정책
- **개인정보/규제 준수**: GDPR, 개인정보보호법, PCI-DSS, HIPAA 등 적용 대상 자동 식별 → 마스킹·암호화·접근 로그 정책에 반영
- **AI 활용 방법**:
  - **Action-Data Mapper**: 화면별 사용자 액션 → CRUD 작업 + 필요 엔티티 자동 도출
  - **API Spec Generator**: OpenAPI 3.x YAML 자동 생성 (요청/응답 스키마, 인증 스킴, 에러 응답, 상태 코드 포함)
  - **Schema Designer**: 정규화/비정규화 트레이드오프 분석 후 권장 스키마 + 근거 제시
  - **ERD Generator**: Mermaid 또는 dbdiagram.io 형식으로 ERD 자동 생성
  - **PII Detector**: 필드명·설명을 분석하여 개인정보·민감정보 자동 식별 → 마스킹/암호화/접근 로그 정책 자동 부여
  - **Index Advisor**: 화면별 조회 패턴 기반 인덱스 후보 제안 (선택성 분석으로 과잉 인덱스 방지)
  - **Migration Planner**: 스키마 변경 시 안전한 마이그레이션 절차(Online DDL → Backfill → Cutover) 자동 작성
  - **검증 도구 통합**: 생성된 OpenAPI는 Spectral 린터, ERD는 Mermaid 파서 통과 후 다음 단계 진입
- **검증 기준**:
  - 모든 사용자 액션이 API 또는 클라이언트 내부 동작으로 분류
  - **각 API에 요청/응답 스키마, 인증/인가, 에러 응답, 상태 코드 정의**
  - **각 테이블에 필드, 타입, 제약조건, 인덱스, 보존 정책 포함**
  - 민감정보는 암호화/마스킹/접근 로그 정책 정의

### 6. 시스템 아키텍처 설계 (System Architecture Design)
- **목적**: 화면설계서와 데이터 설계 결과를 바탕으로 실제 운영 가능한 시스템 구조 결정
- **고려 요소**: 트래픽 규모, 데이터 양/증가 속도, 보안 요구사항, 확장성, 비용, 장애 허용 수준, 외부 연동
- **주요 산출물**: 시스템 구성도, 기술 스택 결정서, 네트워크 구성, 인증/인가 구조, 캐싱·MQ·비동기 처리 전략, 모니터링/로깅 전략
- **AI 활용 방법**:
  - **Capacity Planner**: 트래픽·데이터 증가 시뮬레이션 (피크 RPS, 데이터 증가율, 동시 접속자 수)
  - **Tech Stack Advisor**: 후보 스택 2~3개에 대해 정량 평가 매트릭스(성능/비용/팀 역량/생태계/락인 리스크)를 LLM-as-Judge로 작성 → 권장안 + 근거 제시
  - **Cost Estimator**: 클라우드 사양 + 트래픽 추정 → 월간 비용 자동 계산 (AWS/GCP/Azure pricing API 연동)
  - **Architecture Diagram Generator**: C4 모델(Context/Container/Component/Code) 다이어그램 자동 생성
  - **Resilience Designer**: SPOF 식별 + 회복 전략(재시도/서킷 브레이커/타임아웃/큐잉/멱등성) 자동 제안
  - **Compliance Mapper**: 규제 요구사항 → 아키텍처 결정(데이터 위치, 암호화, 감사 로그) 자동 매핑
  - **프롬프트 전략**: NFR 목록을 입력으로 두고 모든 결정을 NFR ID에 역참조 — 근거 누락 시 재생성
- **검증 기준**:
  - **각 기술 선택에는 대안 비교와 선택 근거 명시**
  - 비기능 요구사항과 아키텍처 결정이 연결
  - 장애 상황에서의 복구 전략이 설명됨

### 7. 보안 설계 (Security Design)
- **목적**: 시스템 전반의 위협 모델링과 보안 통제 설계를 독립 산출물로 정리
- **방법론**: STRIDE(위협 모델링) + OWASP Top 10 + OWASP ASVS
- **주요 산출물**:
  - 위협 모델 다이어그램(Trust Boundary 포함)
  - 인증/인가 설계 (OAuth 2.1, OIDC, RBAC/ABAC)
  - 데이터 암호화 정책 (전송·저장·키 관리)
  - 비밀정보 관리 전략 (Vault, KMS 등)
- **AI 활용 방법**:
  - **Threat Modeler (STRIDE)**: 데이터 흐름도 + 신뢰 경계를 입력으로 STRIDE 카테고리별 위협 자동 도출
  - **OWASP Mapper**: 도출된 위협을 OWASP Top 10 / ASVS 통제 항목과 자동 매핑하여 미완화 영역 식별
  - **Crypto Policy Generator**: 데이터 분류(공개/내부/기밀/극비)별 전송·저장·키 회전 정책 자동 생성
  - **Auth Flow Designer**: OAuth 2.1 / OIDC / RBAC / ABAC 흐름도 자동 작성 + 토큰 수명·갱신 정책 결정
  - **Secret Inventory Agent**: 코드/IaC/구성 파일을 스캔하여 비밀정보 항목 목록화 → Vault/KMS 위치 매핑
  - **Residual Risk Reporter**: 미완화 위험을 잔여 위험 보고서로 정리 (수용/이전/감소/회피 전략 명시)
  - **프롬프트 전략**: 보안 도메인은 환각 비용이 크므로 RAG로 OWASP/NIST/MITRE ATT&CK 문서를 참조 컨텍스트로 강제 주입

### 8. 테스트 설계 (Test Design)
- **목적**: 설계 산출물만으로 구현했을 때 SRS 충족 여부를 검증할 수 있도록 테스트 기준을 사전에 정의
- **주요 산출물**:
  - 인수 기준(Acceptance Criteria)
  - E2E 테스트 시나리오, API 테스트 케이스
  - 권한/보안 테스트, 접근성 테스트 체크리스트
  - 성능 테스트 기준, 회귀 테스트 자동화 전략
- **검증 기준**:
  - **모든 SRS 항목은 최소 1개 이상의 테스트 케이스와 연결**
  - **정상 흐름, 예외 흐름, 권한별 흐름이 모두 포함**
  - **테스트 데이터와 기대 결과가 명확**
- **AI 활용 방법**:
  - **Acceptance Criteria Generator**: 기능명세서 → Gherkin 형식 인수 기준 자동 생성 (Given/When/Then)
  - **Test Case Generator**: Pytest/JUnit/Playwright/Cypress 등 대상 프레임워크별 테스트 코드 초안 자동 생성
  - **Negative Path Generator**: 정상 흐름에서 예외 분기를 역산하여 음성 시나리오 자동 도출 (실패/취소/권한 없음/타임아웃/중복 요청 등)
  - **Test Data Synthesizer**: 합성 데이터 생성 (PII 위험 없는 더미 데이터 + 경계값/극단값 자동 포함)
  - **Coverage Analyzer**: SRS ID ↔ 테스트 ID 매핑 자동 검증 + 미커버 항목 보고
  - **Performance Test Designer**: NFR 성능 기준 → k6/JMeter 시나리오 + 부하 프로파일(Ramp-up/Steady/Stress) 자동 생성
  - **Mutation Testing Designer**: 코드 변형 규칙을 자동 도출하여 테스트 견고성을 평가
  - **프롬프트 전략**: 기능명세서의 정상 흐름과 예외 흐름을 분리하여 각각 별도 Generator에 입력해 누락 차단

### 9. 배포 / 운영(DevOps) 설계 (Deployment & Operations Design)
- **목적**: 시스템을 실제 환경에 안정적으로 배포·운영하기 위한 절차와 기준 정의
- **주요 산출물**:
  - CI/CD 파이프라인 (Build → Test → Scan → Deploy)
  - 환경 구성(dev/staging/production)
  - 배포 전략(blue-green, rolling, canary) + 롤백 절차
  - 로그/모니터링 설계 (RED/USE 메서드)
  - 알림 정책 + 온콜 Runbook
  - SLO/SLI/SLA 정의 + 에러 버짓 정책
  - 백업/복구 정책
- **검증 기준**:
  - **배포 실패 시 롤백 절차가 명확**
  - **핵심 지표(SLI/SLO) 정의**
  - **장애 탐지·알림·대응 책임이 자동화 가능한 수준으로 명시**
- **AI 활용 방법**:
  - **IaC Generator**: 시스템 구성도 → Terraform/Pulumi/CloudFormation 코드 자동 생성
  - **Pipeline Generator**: GitHub Actions / GitLab CI / Argo CD YAML 자동 작성 (Build → Test → SAST/DAST/SCA Scan → Deploy 단계 포함)
  - **Observability Designer**: RED/USE 메서드 기반 메트릭·로그·트레이싱 사양 자동 생성 + Grafana 대시보드 JSON 출력
  - **SLO Calculator**: 가용성 목표(99.9%/99.95% 등) → 분기별 에러 버짓 + 알림 임계치 자동 계산
  - **Runbook Author**: 장애 시나리오별 탐지·격리·복구 절차 자동 생성 (PagerDuty/OpsGenie 연동 정책 포함)
  - **Rollback Strategy Agent**: 배포 전략(Blue/Green·Canary·Rolling)별 롤백 트리거·절차·소요 시간 자동 정의
  - **Chaos Test Designer**: 장애 주입 시나리오(노드 다운/네트워크 지연/디스크 가득)를 자동 설계하여 복원력 검증 절차 생성
  - **검증 도구 통합**: 생성된 Terraform은 `terraform validate` + `tfsec`, 파이프라인은 `actionlint`로 검증 후 머지

---

## 2-A. 공통 AI 활용 패턴 (Cross-stage Patterns)

각 단계에서 공통적으로 적용되는 AI 활용 전략입니다. 단계별 Agent는 아래 패턴을 조합하여 사용합니다.

### Multi-Agent 협업
- **Generator–Reviewer 분리**: 생성 Agent와 검증 Agent를 다른 모델/프롬프트로 분리하여 자기 편향 방지
- **LLM-as-Judge**: 의사결정에는 평가 Agent를 별도로 두고 평가 기준(Rubric)을 사전 정의
- **Self-Consistency**: 동일 입력에 대해 N회 생성 후 다수결로 안정화 (충돌·모호성 판정에 효과적)
- **Debate Pattern**: 두 Agent가 정/반 입장에서 토론 → Judge Agent가 판결 (기술 스택 선정 등 트레이드오프 결정)

### 컨텍스트 관리
- **RAG**: 이전 단계 산출물을 벡터 DB에 적재 → 현 단계 프롬프트에서 검색 주입
- **Long-context 모델**: 1M+ 컨텍스트 모델로 전체 산출물 직접 입력 (RAG 누락 위험 회피)
- **Context Compression**: 컨텍스트 한계 초과 시 요약 + 상세 참조 ID 방식으로 접근
- **Prompt Caching**: 변하지 않는 시스템 프롬프트(템플릿/스타일 가이드)를 캐시로 재사용해 비용 절감

### 출력 형식 강제
- **JSON Schema Constrained Decoding**: 출력이 항상 정해진 스키마를 준수하도록 강제
- **Tool Use**: 외부 도구(Figma MCP, OpenAPI Validator, Pricing API) 호출로 사실 기반 응답 보장
- **Validator-in-the-loop**: 린터·OpenAPI 검사기·Mermaid 파서·`terraform validate` 통과 후에만 다음 단계 진입

### 추론 품질 향상
- **Chain-of-Thought / Tree-of-Thoughts**: 복잡한 분해·설계에서 분기를 명시적으로 평가
- **Chain-of-Verification**: 생성 → 자체 검증 질문 → 답변 확인 → 환각 최소화
- **ReAct**: 추론 ↔ 검색을 반복하여 최신 사실 반영

### 비용·효율 관리
- **모델 라우팅**: 단순 변환은 Haiku, 복잡 추론은 Opus, 비전 분석은 Sonnet 등 작업별 최적 모델 선택
- **배치 처리**: 비동기 가능 산출물(테스트 케이스 일괄 생성 등)은 Batch API로 비용 50% 절감
- **토큰 모니터링**: Cost Monitor Agent가 단계별 토큰 사용량 추적 + 상한 초과 시 알림

---

## 3. 단계 간 연계 및 피드백 루프

기존 폭포수 모델의 한계를 극복하기 위해 **양방향 피드백 루프**를 도입합니다.

```
[단계 N] → 산출물 → [Coverage / Conflict Check Agent] → 통과 시 [단계 N+1]
                                                       → 실패 시 [단계 N 또는 그 이전 단계 재실행]
```

- **Coverage Check Agent**: 이전 단계 산출물의 모든 항목이 현 단계에 반영되었는지 자동 검증
- **Conflict Detection Agent**: 현 단계 산출물과 이전 산출물 간 모순/충돌 자동 탐지
- **재실행 트리거**: 후속 단계에서 결함 발견 시 영향 받는 이전 단계를 자동 재실행하고 변경 이력을 Change Log에 기록
- **버전 충돌/병합 전략**: 여러 Agent가 동시에 산출물 갱신 시 Git의 3-way merge 또는 의미 기반 병합(Semantic Merge) Agent 적용

---

## 4. 표준 입출력 스키마 (Inter-stage Contract)

각 단계의 입출력은 다음 표준 스키마를 따릅니다.

```yaml
artifact:
  id: <단계명-순번>
  stage: <0~9>
  produced_by: <agent_name>
  produced_at: <ISO8601>
  upstream_refs: [<이전 단계 artifact ID 목록>]
  content:
    format: <markdown|json|yaml|figma_link|openapi|...>
    body: <실제 산출물>
  traceability:
    requirement_ids: [<SRS 요구사항 ID 목록>]
  validation:
    coverage_check: <pass|fail>
    conflict_check: <pass|fail>
    custom_checks: [...]
```

이를 통해 모든 산출물이 SRS 요구사항 ID로 추적 가능(Traceability)해집니다.

---

## 5. 요구사항 추적성 매트릭스 (Traceability Matrix)

SRS 충족률 100%를 검증하기 위해 **모든 요구사항이 기능, 화면, API, 데이터, 테스트까지 연결**되어야 합니다.

| SRS ID | 요구사항 요약 | 기능 ID | 화면 ID | API ID | DB Entity | 테스트 ID | 상태 |
|--------|---------------|---------|---------|--------|-----------|-----------|------|
| SRS-001 | 사용자는 이메일로 로그인할 수 있다 | FUNC-001 | UI-LOGIN-001 | API-AUTH-001 | User, Session | TEST-E2E-001 | 설계 완료 |
| SRS-002 | 관리자는 사용자 목록을 조회할 수 있다 | FUNC-010 | UI-ADMIN-001 | API-USER-001 | User | TEST-API-004 | 설계 완료 |

**운영 원칙:**
- SRS ID가 매핑되지 않은 산출물 = 불필요하거나 출처가 불명확한 설계로 간주
- 산출물과 연결되지 않은 SRS ID = 누락 요구사항으로 간주
- 각 단계 종료 시 **Traceability Check Agent**가 자동으로 누락·중복·불일치 검출
- 매칭 알고리즘:
  1. SRS의 모든 요구사항 ID 추출
  2. 각 단계 산출물의 `traceability.requirement_ids` 수집
  3. 미매핑(Orphan) 및 중복 매핑(Duplicate) 자동 탐지
  4. **임베딩 기반 의미 유사도 검사**로 ID 매핑 누락 보완
- 산출물: Coverage Report (통과율, 미매핑 항목, 권장 조치)

---

## 6. 비기능 요구사항(NFR) 관리

비기능 요구사항은 기능 설계나 아키텍처 설계에 흩어져 있으면 누락되기 쉽기 때문에 **별도 목록으로 관리**합니다.

| 분류 | 예시 기준 | 설계 반영 위치 | 검증 방법 |
|------|-----------|----------------|-----------|
| 성능 | 주요 API 응답 300ms 이하 | API 설계, 캐싱 전략, DB 인덱스 | 부하 테스트 |
| 보안 | 모든 민감 API 인증/인가 필수 | API 명세, 보안 설계, 운영 정책 | 보안 테스트, OWASP ASVS |
| 접근성 | WCAG 2.2 AA 준수 | UI 설계 | 접근성 자동 검사 |
| 확장성 | 트래픽 10배 증가 대응 | 아키텍처 설계 | 용량 산정, 부하 테스트 |
| 운영성 | 장애 5분 이내 탐지 | 모니터링/알림 설계 | 장애 시뮬레이션 |
| 개인정보 | 민감정보 암호화 + 접근 로그 | 데이터 설계, 보안 정책 | 정책 리뷰, 감사 로그 검증 |
| 국제화 | 다국어/RTL/통화/날짜 처리 | UI 설계, 데이터 설계 | i18n 자동 검사 |
| 비용 | 월간 인프라 비용 상한 | 아키텍처 설계 | 비용 시뮬레이션 |

---

## 7. AI 산출물 검증 체계 (Multi-Agent Verification)

완전 자동화 설계에서는 **AI가 생성한 산출물을 다른 AI가 독립적으로 검증**하는 구조가 필요합니다.

| Agent 역할 | 책임 |
|------------|------|
| **Generator Agent** | 각 단계의 1차 산출물 생성 |
| **Reviewer Agent** | 산출물의 논리적 오류, 모호성, 누락 검토 |
| **SRS Coverage Agent** | SRS 항목과 산출물 간 매핑 검증 |
| **Conflict Detection Agent** | 단계 간 모순·충돌 탐지 |
| **Risk Agent** | 보안, 성능, 운영, 개인정보 리스크 식별 |
| **Consistency Agent** | 단계 간 용어, ID, 정책, 데이터 구조 일관성 검증 |
| **Test Agent** | 요구사항별 검증 가능성 및 테스트 케이스 도출 |
| **Persona Agent** | 사용자 페르소나로 프로토타입/UI 흐름 시뮬레이션 |
| **Cost Monitor Agent** | 단계별 토큰 사용량 모니터링 + 비용 상한 알림 |

**검증 원칙:**
- **생성 Agent와 검증 Agent는 역할 분리** (자기 검증 금지)
- 검증 결과는 이슈 로그로 남기고 해결 여부를 추적
- **이슈가 남아 있는 단계는 다음 단계의 입력으로 사용 불가**
- 사람이 개입하지 않는 대신, **Agent 간 교차 검증을 필수 품질 게이트로 사용**

---

## 8. 공통 품질 게이트 (Quality Gates)

각 설계 단계는 다음 7가지 조건을 모두 통과해야 다음 단계로 진행할 수 있습니다.

| 게이트 | 검증 항목 |
|--------|-----------|
| **완전성** | 이전 단계 산출물이 모두 반영되었는가 |
| **추적성** | SRS ID와 산출물 ID가 연결되어 있는가 |
| **일관성** | 용어, 역할, 데이터명, 화면명, API명이 단계 간 충돌하지 않는가 |
| **검증 가능성** | 테스트 또는 리뷰로 성공/실패를 판단할 수 있는가 |
| **예외 처리** | 실패, 권한 없음, 데이터 없음, 취소, 중복 요청 등 예외 흐름이 정의되었는가 |
| **보안성** | 인증, 인가, 개인정보, 감사 로그 기준이 반영되었는가 |
| **운영성** | 배포, 모니터링, 장애 대응에 필요한 정보가 포함되었는가 |

각 게이트는 **자동화된 Check Agent로 측정**되며, 1개라도 실패하면 다음 단계로 진행하지 않습니다.

---

## 9. 횡단 관심사 (Cross-cutting Concerns)

- **접근성(a11y)**: 모든 UI 산출물에 WCAG 2.2 AA 자동 검증
- **국제화(i18n)**: 다국어 리소스 키 추출 + RTL/통화/날짜/숫자 형식 자동화
- **디자인 시스템**: 사전 정의된 컴포넌트 라이브러리 사용 강제
- **개인정보/규제**: GDPR, 개인정보보호법, PCI-DSS, HIPAA 등 적용 대상 자동 식별
- **AI 사용 비용 관리**: 단계별 토큰 사용량 모니터링 + 비용 상한 알림
- **버전 관리**: 모든 산출물 Git 기반 버전 기록 + AI 자동 Change Log 생성

---

## 10. 산출물 디렉토리 구조 및 변경 관리

모든 설계 산출물은 Git에 버전 기록되며, AI가 자동으로 Change Log를 생성합니다.

**권장 산출물 구조:**

```text
/docs
  /00-srs
  /01-functional-spec
  /02-wireframe
  /03-prototype-feedback
  /04-ui-spec
  /05-data-design
  /06-architecture
  /07-security
  /08-test-design
  /09-operations
  /traceability       # 추적성 매트릭스
  /change-log         # 자동 생성된 변경 이력
  /quality-gates      # 게이트 통과/실패 보고서
```

**변경 관리 원칙:**
- 모든 변경은 **변경 사유, 영향 범위, 연결된 SRS ID**를 포함
- **SRS 변경 시 연결된 기능/화면/API/DB/테스트를 자동 재검토**
- 변경 영향 분석이 끝나기 전에는 다음 단계 산출물을 확정하지 않음
- 변경 시 영향 받는 단계는 자동 재실행되며 Change Log에 기록

---

## 11. 전체 프로세스 공통 원칙

- **순서 의존성 철저 준수**: 각 단계의 산출물은 반드시 이전 단계 결과를 입력으로 사용
- **사람 개입 최소화**: 자동화 성숙도 Level에 따라 사람의 역할이 점진적으로 축소
- **버전 관리**: 모든 산출물은 Git에 버전 기록 (AI가 자동 Change Log 생성)
- **자동 재실행**: SRS Coverage Check / Quality Gate 실패 시 영향 단계를 자동 재실행

---

## 12. 최종 검증 체크리스트

최종 설계 산출물은 다음 기준을 모두 만족해야 합니다.

- [ ] 설계 산출물만으로 개발팀(또는 AI 개발 에이전트)이 구현 가능
- [ ] SRS 대비 **불일치 항목 0건**
- [ ] SRS 대비 **미매핑 항목 0건**
- [ ] **기능별 정상/예외/권한 흐름** 정의 완료
- [ ] **화면별 상태 UI**(loading/empty/error/disabled/success) 정의 완료
- [ ] **API별 요청/응답/에러/권한** 정의 완료
- [ ] **DB별 제약조건/인덱스/보존 정책** 정의 완료
- [ ] **모든 요구사항에 테스트 케이스 연결** 완료
- [ ] **위협 모델 + 보안 통제** 정의 완료
- [ ] **배포/롤백/모니터링/장애 대응 절차** 정의 완료
- [ ] **7가지 품질 게이트** 모두 통과
- [ ] 단계별 **AI 비용/토큰 사용량**이 상한 이내

---

## 13. 기대 효과

- 설계 산출물의 **추적 가능성(Traceability) 100%**
- 단계 간 **모순 0건** 및 **누락 0건**
- **사람 개입 시간** 단계별 평균 30분 이하 (Level 4 기준)
- 동일한 SRS 입력 시 **재현 가능한** 산출물 생성
- 운영 단계까지 포함한 **End-to-End 자동화** 달성
- **단일 AI의 환각/누락 리스크**를 Multi-Agent 교차 검증으로 최소화
