# Harmonia CDIX — 임원 요약

**한 줄 정의:** 외부기관에서 반입한 PDF·이미지 검사결과를 자동으로 구조화해 병원 EMR에서 쓸 수 있는 데이터로 바꾸는 **병원 전용 문서 운영 자동화 플랫폼**입니다.

**영문:** Turning external result documents into hospital-ready data.

---

## 왜 필요한가

타 기관 검사 결과는 팩스·스캔·비표준 양식으로 들어와 **수기 입력과 지연**을 만듭니다. 단순 OCR만으로는 비정형·다문서·저신뢰 구간에서 한계가 있어, **문서 정규화 → 분류·추출 규칙 → 검토·승인 → EMR 반영**까지 이어지는 **운영 체계**가 필요합니다.

## 무엇을 제공하는가

- **반입·표준화:** 다양한 형식의 파일을 안정적으로 받고, 스캔 품질을 맞춘 뒤 OCR합니다.
- **구조화:** 기관·문서유형별 프로토콜(규칙)로 핵심 항목을 추출하고, 필요 시 AI로 보조합니다.
- **임상 데이터화:** 검사명·단위·코드 정규화로 **EMR·FHIR 등 연계**에 맞는 데이터를 만듭니다.
- **통제 가능한 품질:** 낮은 신뢰도 항목은 **사람 검토(Human-in-the-loop)** 후 확정·전송합니다.
- **거버넌스:** RBAC, MFA, 암호화, 감사·Provenance 등 **의료기관 수준의 보안·추적**을 전제로 합니다.

## MVP 범위 (1차)

| 포함 | 제외 (이후 단계) |
|------|------------------|
| PDF/JPG/PNG 업로드, 서버 OCR, 규칙 기반 추출, JSON·다운로드/API/DB 적재 | LLM 요약, 자유서술 의미해석, 완전 자동 환자매칭, 복잡 병리/서술, FHIR 전 스펙 |

**전달 형태:** 웹 기반, 서버 OCR 후 DB 반영, 웹 확인 또는 **EMR 연동(API, HL7)**.

## 기대 효과

- 외부 검사 결과 **재입력 시간·오류 감소**, 진료·수납·CS 흐름의 **지연 완화**
- **추적 가능한** 처리 이력(누가 승인했는지, 어떤 규칙/모델 버전인지)
- 향후 **FHIR(DocumentReference, Observation, DiagnosticReport 등)** 중심 저장·연계로 확장 가능한 기반

## 상세 설계 문서

기술·운영 설계의 전체 내용은 **`Docs/`** 폴더에 주제별로 나누어 두었습니다.

| 문서 | 내용 |
|------|------|
| [Docs/README.md](Docs/README.md) | 문서 목록 및 읽는 순서 안내 |
| [Docs/01-product-overview.md](Docs/01-product-overview.md) | 슬로건, 벤치마크(TESSER Ontol, Infinx) |
| [Docs/02-mvp-and-platform.md](Docs/02-mvp-and-platform.md) | MVP 범위·개발 스펙 |
| [Docs/03-architecture-layers.md](Docs/03-architecture-layers.md) | 수집부터 모델 운영·예외처리까지 레이어 설계 |
| [Docs/04-workflow-fhir-routine.md](Docs/04-workflow-fhir-routine.md) | 운영 모델, FHIR 리소스 매핑 루틴 |
| [Docs/05-fhir-example-scenario.md](Docs/05-fhir-example-scenario.md) | 혈액검사 예시·Bundle JSON·Provenance/AuditEvent 메모 |
| [Docs/06-hardware-recommendations.md](Docs/06-hardware-recommendations.md) | PoC/납품/상급형 하드웨어 옵션 |
