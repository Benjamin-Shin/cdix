# 운영 모델 및 FHIR 매핑 루틴

## 운영 모델

1) 문서 반입 → 
2) 환자 후보 매칭 → 
3) 문서 분류/추출 → 
4) 사용자 검토/수정 → 
5) FHIR 저장 → 
6) EMR 반영.

## 최종 루틴

* 원본 보관: DocumentReference
* 환자 연결: Patient
* 진료 맥락 연결: Encounter가 있으면 연결
* 개별 항목 저장: Observation
* 문서 단위 결과 묶음: DiagnosticReport
* 누가/어떻게 만들었는지: Provenance
* 누가 보고 수정/전송했는지: AuditEvent
* 검토 대기/승인/반려 상태: Task

