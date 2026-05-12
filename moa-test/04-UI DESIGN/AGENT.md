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