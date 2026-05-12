- **목적**: 이해관계자 인터뷰, 도메인 분석, 경쟁 제품 분석을 통해 요구사항을 보완
    
- **방법**:
    
    - **Interview Agent**: 의사결정 트리 기반 동적 질문 생성 — 이전 답변에 따라 후속 질문을 분기 (예: "B2C SaaS" 답변 시 사용자 페르소나 5종 자동 도출 후 각각 인터뷰)
        
    - **Stakeholder Persona Agent**: CEO·도메인 전문가·최종 사용자·규제 담당자 역할을 시뮬레이션해 단일 인터뷰에서 빠지는 관점을 보완
        
    - **Domain Research Agent**: 웹 검색 + 경쟁 제품 분석(RAG)으로 업계 표준·규제·관행을 자동 반영
        
    - **SRS Generator**: IEEE 830 / ISO/IEC/IEEE 29148 템플릿 + Few-shot 예시로 표준 형식 변환. 각 항목에 ID, 우선순위(MoSCoW), 검증 기준, 출처를 자동 부여
        
    - **Conflict Detection Agent**: LLM-as-Judge로 항목 간 의미 충돌·중복·모호성 판정 (Self-Consistency: 동일 항목 3회 평가 후 다수결)
        
    - **프롬프트 전략**: Chain-of-Verification(생성 → 검증 질문 자체 생성 → 자체 답변)으로 환각 최소화
        
- **검증 기준**: SRS 항목 간 모호성·중복·충돌 0건 (Conflict Detection Agent 자동 검수)
    
- **입출력 포맷**: Markdown + 구조화된 YAML(요구사항 ID, 우선순위, 검증 기준 포함)