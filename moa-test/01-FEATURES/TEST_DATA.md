# 테스트 데이터

기준 문서: `TEST_SCENARIOS.md`, `NFR_VERIFICATION_PLAN.md`

## 1. 테스트 계정

| 계정 ID | 역할 | 이메일 | 비밀번호 | 용도 |
| --- | --- | --- | --- | --- |
| U-001 | 일반 사용자 | user.viewer@example.com | Test!2345 | 조회, 질의, 업로드 기본 테스트 |
| U-002 | 설비 제어자 | user.operator@example.com | Test!2345 | 설비 제어 명령 실행 테스트 |
| U-003 | 승인자 | user.approver@example.com | Test!2345 | 고위험 명령 승인 테스트 |
| U-004 | 관리자 | admin@example.com | Admin!2345 | 관리자 페이지, 권한, 로그, 모델 관리 |
| U-005 | 잠금 테스트 사용자 | locktest@example.com | Lock!2345 | 로그인 실패 잠금 테스트 |

## 2. 알람 샘플

| 알람 ID | 알람 코드 | 유형 | 심각도 | 설비명 | 발생시각 | 담당자 |
| --- | --- | --- | --- | --- | --- | --- |
| ALM-001 | TEMP-HIGH-001 | 온도 | Critical | EQP-A01 | 2026-05-12 09:00:00 | user.operator@example.com |
| ALM-002 | PRESS-LOW-002 | 압력 | Major | EQP-B02 | 2026-05-12 09:05:00 | user.operator@example.com |
| ALM-003 | SENSOR-OFF-003 | 센서 | Minor | EQP-C03 | 2026-05-12 09:10:00 | user.viewer@example.com |
| ALM-004 | INFO-MAINT-004 | 유지보수 | Info | EQP-D04 | 2026-05-12 09:15:00 | admin@example.com |
| ALM-999 | UNKNOWN-999 | 미등록 | Major | EQP-Z99 | 2026-05-12 09:20:00 | admin@example.com |

## 3. 알람 가이드 샘플

| 가이드 ID | 알람 코드 | 최소 유사도 | 요약 | 표준 조치 |
| --- | --- | --- | --- | --- |
| GUIDE-001 | TEMP-HIGH-001 | 0.82 | 설비 온도 상한 초과 | 냉각수 밸브 상태 확인, 히터 출력 점검, 센서 보정 확인 |
| GUIDE-002 | PRESS-LOW-002 | 0.79 | 공압 라인 압력 저하 | 공급 밸브 확인, 누설 검사, 압력 센서 상태 확인 |
| GUIDE-003 | SENSOR-OFF-003 | 0.76 | 센서 신호 미수신 | 케이블 체결, 센서 전원, I/O 모듈 상태 확인 |

## 4. 설비 제어 명령 샘플

| 명령 ID | 자연어 명령 | 변환 명령 | 위험 등급 | 필요 권한 | 기대 결과 |
| --- | --- | --- | --- | --- | --- |
| CMD-001 | A01 냉각팬 켜줘 | EQP-A01.FAN=ON | Low | 설비 제어자 | 재확인 후 전송 |
| CMD-002 | B02 압력 보정 시작해 | EQP-B02.CALIBRATE_PRESSURE=START | Medium | 설비 제어자 | 재확인 후 전송 |
| CMD-003 | A01 라인 정지해 | EQP-A01.LINE_STOP=ON | High | 설비 제어자 + 승인자 | 추가 승인 필요 |
| CMD-004 | 전체 설비 정지 | ALL_EQUIPMENT.STOP=ON | High | 관리자 + 승인자 | 추가 승인 필요 |
| CMD-005 | 권한 없는 사용자의 팬 제어 | EQP-A01.FAN=ON | Low | 설비 제어자 | 일반 조회자는 차단 |

## 5. 업로드 파일 샘플

| 파일 ID | 파일명 | 확장자 | 크기 | 기대 결과 |
| --- | --- | --- | --- | --- |
| FILE-001 | equipment_manual.pdf | pdf | 5MB | 허용 |
| FILE-002 | sop_line_a.docx | docx | 2MB | 허용 |
| FILE-003 | alarm_history.csv | csv | 1MB | 허용 |
| FILE-004 | troubleshooting.md | md | 500KB | 허용 |
| FILE-005 | equipment_photo.jpg | jpg | 3MB | 허용 |
| FILE-006 | installer.exe | exe | 10MB | 거부 |
| FILE-007 | oversized_manual.pdf | pdf | 51MB | 거부 |
| FILE-008 | invalid_mime.pdf | pdf | 1MB | MIME 불일치 시 거부 |

## 6. VectorDB 샘플

| DB ID | 별칭 | 소유자 | 공유 범위 | 기대 결과 |
| --- | --- | --- | --- | --- |
| VDB-001 | A라인 매뉴얼 | U-001 | 개인 | 생성 허용 |
| VDB-002 | 공정 공통 SOP | U-004 | 전체 | 생성 허용 |
| VDB-003 | B라인_트러블슈팅 | U-002 | 그룹 | 생성 허용 |
| VDB-004 | A | U-001 | 개인 | 별칭 길이 미달로 거부 |
| VDB-005 | 금지문자!* | U-001 | 개인 | 금지 문자로 거부 |

## 7. 질의 샘플

| 질의 ID | 입력 | 목적 | 기대 결과 |
| --- | --- | --- | --- |
| Q-001 | 지난 7일 A01 설비 Major 이상 알람 보여줘 | 이벤트 이력 조회 | 조건 추출 후 시간순 이력 제공 |
| Q-002 | TEMP-HIGH-001 알람 원인과 조치 방법 알려줘 | 알람 원인 제공 | 원인, 조치 절차, 근거 제공 |
| Q-003 | A라인 냉각수 밸브 점검 절차는? | 도메인 답변 | Top-K 문서 기반 답변 제공 |
| Q-004 | 지난달 B라인 압력 평균을 차트로 보여줘 | 자연어 통계 분석 | 평균값과 차트 제공 |
| Q-005 | 내가 올린 SOP 문서에서만 답해줘 | 데이터 소스 제한 | 지정 VectorDB 내 검색 |
| Q-006 | 관련 없는 문서에 대한 질문 | 근거 부족 | 근거 부족 안내 |

## 8. 음성 샘플

| 음성 ID | 발화 | 목적 | 기대 결과 |
| --- | --- | --- | --- |
| VOICE-001 | 하이 모아 | 핫워드 호출 | STT 활성화 |
| VOICE-002 | TEMP-HIGH-001 알람 원인 알려줘 | 음성 질의 | 텍스트 변환 후 질의 실행 |
| VOICE-003 | 설비 대시보드 열어줘 | 웹 사이트 호출 | 허용 URL 열림 |
| VOICE-004 | 등록되지 않은 사이트 열어줘 | 미등록 발화 | 실행하지 않고 안내 표시 |

## 9. 웹 사이트 호출 매핑

| 매핑 ID | 발화문 | URL | 허용 여부 |
| --- | --- | --- | --- |
| URL-001 | 설비 대시보드 열어줘 | https://intranet.example.com/equipment-dashboard | 허용 |
| URL-002 | 알람 현황 열어줘 | https://intranet.example.com/alarms | 허용 |
| URL-003 | 외부 검색 열어줘 | http://external.example.com/search | 거부 |

## 10. 리포팅 샘플

| 리포트 ID | 포맷 | 포함 필드 | 기대 결과 |
| --- | --- | --- | --- |
| RPT-001 | PDF | 질의, 답변, 근거, 생성 시각, 데이터 소스, 분석 결과 | PDF 생성 |
| RPT-002 | CSV | 질의, 답변, 근거, 생성 시각, 데이터 소스, 분석 결과 | UTF-8 with BOM CSV 생성 |
| RPT-003 | XLSX | 질의, 답변, 근거, 생성 시각, 데이터 소스, 분석 결과 | XLSX 생성 |

## 11. 백업/복구 샘플

| 백업 ID | 대상 | 스케줄 | 보존 | 기대 결과 |
| --- | --- | --- | --- | --- |
| BAK-001 | 원본 문서 | 매일 02:00 | 30일 | 백업 생성 |
| BAK-002 | 임베딩 벡터데이터 | 매일 02:00 | 30일 | 백업 생성 |
| BAK-003 | 메타데이터 | 매일 02:00 | 30일 | 백업 생성 |
| BAK-004 | 사용자 설정값 | 매일 02:00 | 30일 | 백업 생성 |
| BAK-005 | 전체 데이터 자산 | 매월 1일 03:00 | 12개월 | 전체 백업 생성 |

## 12. 성능 테스트 입력

| 세트 ID | 동시 사용자 | 질의 수 | 질의 유형 | 합격 기준 |
| --- | --- | --- | --- | --- |
| PERF-001 | 1 | 30 | 일반 텍스트 질의 | p95 10초 이내 |
| PERF-002 | 5 | 100 | 일반 텍스트 질의 | p95 10초 이내 |
| PERF-003 | 5 | 50 | 음성 질의 | STT 시작부터 TTS 재생 시작까지 p95 20초 이내 |
