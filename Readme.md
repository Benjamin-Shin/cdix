# Harmonia CDIX

## 한글 슬로건

> 다양한 검사 결과물과 문서 이미지를 의미 있는 임상 데이터로 전환해 연계하는 지능형 교환 허브.

> Harmonia CDIX는 외부기관 반입 PDF/이미지 결과지를 구조화 데이터로 전환하는 병원전용 문서 운영자동화 플랫폼입니다.

> 외부 반입 결과지를 데이터로 바꾸는 병원전용 플랫폼

> Harmonia CDIX는 외부기관에서 반입되는 PDF 및 이미지 형태의 검사결과 문서를 자동 분류·추출·정규화하여, 병원 EMR에서 활용 가능한 구조화 데이터로 전환하는 문서 운영자동화 플랫폼입니다.

## 영어 슬로건

> Turning external result documents into hospital-ready data.

> Harmonia CDIX is a hospital-focused document automation platform that transforms externally received PDF and image-based diagnostic result documents into structured data ready for EMR use.

> Harmonia CDIX converts externally received diagnostic documents into meaningful, structured clinical data for hospital workflows.

## 벤치마크

### TESSER Ontol  https://clinic.ontol.com/#about

* 앱은 환자가 검사지, 판독소견, DICOM등 결과를 입력하면 쉬운용어로 알기쉽게 설명해주는 용도
* B2B는 검진센터 위주로 결과서 소견을 AI로 작성 및 환자 전달까지의 시스템을 제공

### Infinx https://www.infinx.com/document-capture-plus-ai-and-referral-management/

* 많은 기업들이 OCR 도구를 활용하여 문서 처리를 자동화하거나, 직원들이 수동으로 전자 의료 기록(EMR) 시스템을 검토하고 업데이트하지만, 이러한 워크플로는 복잡하고 비정형적인 대량의 데이터를 관리할 때는 종종 한계를 드러냅니다.
  * OCR은 여러 문서가 포함된 팩스, 손으로 쓴 양식, 비표준 레이아웃에서 어려움을 겪어 데이터 추출이 불완전한 경우가 발생합니다.
  * 수동 데이터 입력은 환자 접수, 예약 및 청구 업무 흐름을 지연시킵니다.
  * 기존 워크플로는 프로세스를 완전히 자동화하지 못하여 중요한 환자 및 진료비 데이터가 EMR 및 청구 시스템과 연결되지 않은 상태로 남아 있습니다.

## Layer

### 수집/반입 레이어

* PDF, JPG, PNG, TIFF 파일
* 스캔 입력
* 폴더 감시, SFTP, 메일, API반입
* 파일형식 판별, 메타데이터 추출, 원본 보관

> 이건 생각보다 중요하다. 입력원이 병원마다 제각각이라서, OCR 전에 파일을 안정적으로 받는 수집 계층이 먼저 있어야 한다. Apache Tika는 다양한 파일 형식 탐지와 메타데이터/텍스트 추출을 단일 인터페이스로 제공하는 쪽이라, 이런 전처리·형식 판별 계층의 참고축으로 적합하다.

### 문서 표준 레이어

* PDF rasterize
* 이미지 deskew, denoise, contrast, crop
* 방향 보정, 페이지 분리
* 멀티페이지 묶음 처리
* 원본/표준화본 버전 관리

> 이 레이어가 없으면 OCR 정확도가 흔들린다. 특히 외부기관 반입 결과지는 해상도, 기울기, 배경, 컬러 상태가 제각각이라 문서 정규화 엔진이 사실상 별도 제품 수준으로 필요하다.

### OCR 레이어

* 기본 OCR 엔진
* 한글/영문/숫자 혼합 인식
* 표/셀 구조 보조
* 좌표 기반 텍스트 박스 추출
* confidence score 저장

> Tesseract는 LSTM 기반 OCR 엔진을 제공하고 100개 이상의 언어와 35개 이상의 스크립트를 지원한다. 병원 문서에서는 이걸 단독으로 끝내기보다 baseline OCR 엔진으로 쓰고, 숫자/단위/표 인식 보정기를 위에 얹는 방식이 현실적이다.

### 문서 분류·프로토콜 레이어

* 문서 유형 정의
  * 예: 혈액검사 결과지 / 병리 결과지 / 영상 판독지
* 기관별 템플릿 버전 관리
* 앵커 텍스트, 영역, 키워드 규칙 설정
* 필드 매핑 규칙
* 예외 규칙
* 테스트 샘플셋 관리
* 배포/롤백

> 즉 이건 그냥 “프로토콜 생성기”보다 Document Protocol Studio 또는 Extraction Rule Studio 같은 별도 서브모듈로 보는 게 맞다. 초반에는 AI가 모든 문서를 알아서 이해하는 게 아니라, 기관별/문서유형별 프로토콜을 사람이 정의하고 AI가 보조하는 구조가 성공률이 훨씬 높다.

### 추출·해석 레이어

이 레이어는 두 갈래가 필요하다.

A. 규칙 기반

* 고정 영역
* 키워드 앵커
* 표/행/열 매핑
* 날짜/단위/수치 정규식

B. AI 기반

* 문서 유형 재분류
* 자유서술 영역 요약
* 필드 후보 추천
* 애매한 항목 disambiguation
* 사람이 만든 프로토콜 초안 자동 제안

> 여기서 로컬 LLM은 “OCR 대체”보다 후처리 해석 엔진으로 쓰는 게 맞다. 폐쇄망에서는 vLLM처럼 OpenAI 호환 API 서버로 띄우는 방식이나, llama.cpp처럼 경량 HTTP 서버를 두는 방식이 현실적이다. vLLM은 오프라인 문서 접근 옵션과 OpenAI 호환 Chat/Responses API를 제공하고, llama.cpp도 로컬 HTTP 서버와 OpenAI 호환 엔드포인트를 제공한다.

### 정규화/표준용어 레이어

* 검사명 표준화
* 단위 통합
* 참조범위 파싱
* 결과값 타입 구분
* 수치 / 양성음성 / 서술형
* 동일 검사 alias 매핑
* 가능하면 LOINC, 내부 코드, EMR 항목코드 매핑

> OCR로 글자를 읽는 것과 EMR에서 쓸 수 있는 데이터는 완전히 다르다. 예를 들어 “HbA1c”, “당화혈색소”, “Glycated Hb”, “A1C”가 같은 항목인지 모르면 연계가 불가능하다. 즉 CDIX에는 Terminology Mapper가 있어야 한다.

### Human-in-the-loop 검토 레이어

* 원문과 추출 결과 좌우 비교
* confidence 낮은 항목 강조
* 수정 이력 저장
* 승인/반려/재처리
* 검토자 서명 또는 확정
* 예외 큐 분리

> CDIX가 초기에 성공하려면 “완전자동”보다 자동 추출 + 검토 확정 + EMR 반영 흐름이 훨씬 현실적이다.

### 워크플로우/자동화 레이어

* 접수
* 환자 매칭
* 분류
* OCR
* 추출
* 정규화
* 검토 요청
* 승인
* EMR 전송
* 전송 결과 확인
* 실패 재시도

> 즉 단순 OCR 솔루션이 아니라 BPM + Queue + State Machine이 있어야 한다. 문서마다 상태가 명확히 보여야 한다. 예: RECEIVED → PREPROCESSED → OCR_DONE → EXTRACTED → REVIEW_REQUIRED → APPROVED → SENT → ACKNOWLEDGED

### EMR 연계 레이어

* DB insert
* REST API
* HL7 v2
* FHIR
* 파일 export
* 인터페이스 엔진 연동

> 문서 자체를 보관/참조하려면 FHIR DocumentReference가 맞고, 문서가 어떤 변환 과정을 거쳐 최종 데이터가 되었는지는 Provenance, 운영/보안 로그는 AuditEvent가 잘 맞는다. HL7 FHIR는 DocumentReference를 PDF, 스캔 문서, JPEG/GIF/TIFF 같은 이미지 파일까지 포괄하는 문서 인덱싱 리소스로 설명한다.

### IAM / 보안 레이어

* RBAC (역할기반 권한)
* MFA (이중 인증)
* SSO (OIDC/SAML)
* 세션 정책
* 비밀번호 정책
* 부서/역할 기반 접근통제
* 원문 보기 권한 분리
* 승인권한 분리
* 서비스 계정 분리

* 문서 원본 암호화
* 전송구간 TLS
* 저장구간 암호화
* 비밀키/자격증명 vault
* 감사로그 위변조 방지
* 원문 다운로드 통제
* 마스킹 정책
* IP/망 분리 정책

> RBAC와 MFA는 필수다. MFA는 TOTP나 WebAuthn/passkey까지 염두에 두는 게 좋고, OIDC 기반 IdP를 두면 운영이 편하다. Keycloak은 WebAuthn 등록, passwordless/2FA, recovery codes 같은 흐름을 공식적으로 제공한다.

### 감사추적 / 증적 레이어

* 누가 업로드했는지
* 누가 수정했는지
* 누가 승인했는지
* 어떤 모델/룰 버전이 사용됐는지
* 어떤 필드가 AI 생성인지, 규칙 추출인지
* 언제 EMR로 전송됐는지
* 결과 ACK/NACK

> FHIR AuditEvent는 운영, 프라이버시, 보안, 유지보수, 성능분석을 위한 이벤트 기록 리소스로 정의돼 있고, Provenance는 리소스가 어떤 엔티티와 프로세스를 거쳐 생성·변형됐는지 남기는 데 쓰인다. CDIX엔 둘 다 필요하다.

### 관측성 / 모니터링

* 처리량
* 페이지당 OCR 시간
* 추출 성공률
* 문서유형별 정확도
* 기관별 실패율
* EMR 전송 실패율
* GPU/CPU 사용량
* 큐 적체량

> OpenTelemetry는 traces, metrics, logs를 수집·내보내는 표준 관측성 프레임워크라, 이런 멀티서비스 구조에 잘 맞는다.

### 모델 운영 레이어

* 모델 레지스트리
* 프롬프트 버전 관리
* 추출 스키마 버전 관리
* 룰 버전 관리
* 평가셋/벤치셋
* 문서유형별 정확도 리포트
* 롤백

> 즉 CDIX는 사실상 LLM 앱이 아니라 문서 AI 운영 플랫폼이다.

### 예외 처리 / 재처리 레이어

* OCR 실패
* 기관 미분류
* 환자 매칭 실패
* 문서 파손
* 다중 결과지 묶음 반입
* 신뢰도 낮음
* EMR 전송 실패
* 중복 반입

> 각 예외는 별도 큐로 빠지고, 사람이 다시 태워야 한다.

### 환자·문서 매칭 레이어

* 환자명
* 생년월일
* 성별
* 외부기관 등록번호
* 검사일
* 병원 EMR 환자번호
* 수동/반자동 매칭
* 중복 방지

> 외부기관 결과지는 같은 이름, 잘못된 스캔, 식별자 누락 문제가 있어서 MPI 수준까지는 아니어도 환자 매칭 서브시스템이 반드시 필요하다.

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

## 예시 시나리오

외부기관에서 PDF 결과지 1장이 들어왔고, CDIX가 아래를 추출했다고 가정할게.

* 환자: Patient/12345
* 결과지 제목: 외부 혈액검사 결과지
* 검사일: 2026-04-08
* 항목 1: HbA1c = 7.4 %
* 항목 2: Creatinine = 1.21 mg/dL
* 검토자 확인 완료

```json
{
  "resourceType": "Bundle",
  "type": "transaction",
  "timestamp": "2026-04-09T10:15:00+09:00",
  "entry": [
    {
      "fullUrl": "urn:uuid:docref-1",
      "resource": {
        "resourceType": "DocumentReference",
        "identifier": [
          {
            "system": "https://harmonia.example/fhir/identifier/ingest-id",
            "value": "ING-20260409-0001"
          }
        ],
        "status": "current",
        "docStatus": "final",
        "type": {
          "coding": [
            {
              "system": "https://harmonia.example/fhir/CodeSystem/document-type",
              "code": "external-lab-result",
              "display": "External Lab Result"
            }
          ],
          "text": "외부기관 혈액검사 결과지"
        },
        "category": [
          {
            "text": "Laboratory"
          }
        ],
        "subject": {
          "reference": "Patient/12345"
        },
        "date": "2026-04-09T09:58:00+09:00",
        "author": [
          {
            "reference": "Organization/ext-lab-01",
            "display": "외부검사기관 A"
          }
        ],
        "custodian": {
          "reference": "Organization/hospital-01",
          "display": "도입병원"
        },
        "description": "외부기관 반입 혈액검사 결과지 원본",
        "content": [
          {
            "attachment": {
              "contentType": "application/pdf",
              "title": "external-lab-result-20260408.pdf",
              "url": "Binary/ext-doc-0001"
            }
          }
        ],
        "context": {
          "period": {
            "start": "2026-04-08T08:30:00+09:00",
            "end": "2026-04-08T08:30:00+09:00"
          },
          "practiceSetting": {
            "text": "진단검사의학"
          }
        }
      },
      "request": {
        "method": "POST",
        "url": "DocumentReference"
      }
    },
    {
      "fullUrl": "urn:uuid:obs-hba1c",
      "resource": {
        "resourceType": "Observation",
        "identifier": [
          {
            "system": "https://harmonia.example/fhir/identifier/extracted-item-id",
            "value": "ING-20260409-0001-HBA1C"
          }
        ],
        "status": "final",
        "category": [
          {
            "text": "Laboratory"
          }
        ],
        "code": {
          "coding": [
            {
              "system": "https://harmonia.example/fhir/CodeSystem/lab-item",
              "code": "HBA1C",
              "display": "HbA1c"
            }
          ],
          "text": "당화혈색소(HbA1c)"
        },
        "subject": {
          "reference": "Patient/12345"
        },
        "effectiveDateTime": "2026-04-08T08:30:00+09:00",
        "issued": "2026-04-09T10:15:00+09:00",
        "performer": [
          {
            "reference": "Organization/ext-lab-01",
            "display": "외부검사기관 A"
          }
        ],
        "valueQuantity": {
          "value": 7.4,
          "unit": "%",
          "system": "http://unitsofmeasure.org",
          "code": "%"
        },
        "interpretation": [
          {
            "text": "High"
          }
        ],
        "referenceRange": [
          {
            "text": "4.0 - 6.0 %"
          }
        ]
      },
      "request": {
        "method": "POST",
        "url": "Observation"
      }
    },
    {
      "fullUrl": "urn:uuid:obs-creatinine",
      "resource": {
        "resourceType": "Observation",
        "identifier": [
          {
            "system": "https://harmonia.example/fhir/identifier/extracted-item-id",
            "value": "ING-20260409-0001-CRE"
          }
        ],
        "status": "final",
        "category": [
          {
            "text": "Laboratory"
          }
        ],
        "code": {
          "coding": [
            {
              "system": "https://harmonia.example/fhir/CodeSystem/lab-item",
              "code": "CRE",
              "display": "Creatinine"
            }
          ],
          "text": "크레아티닌"
        },
        "subject": {
          "reference": "Patient/12345"
        },
        "effectiveDateTime": "2026-04-08T08:30:00+09:00",
        "issued": "2026-04-09T10:15:00+09:00",
        "performer": [
          {
            "reference": "Organization/ext-lab-01",
            "display": "외부검사기관 A"
          }
        ],
        "valueQuantity": {
          "value": 1.21,
          "unit": "mg/dL",
          "system": "http://unitsofmeasure.org",
          "code": "mg/dL"
        },
        "referenceRange": [
          {
            "text": "0.7 - 1.2 mg/dL"
          }
        ]
      },
      "request": {
        "method": "POST",
        "url": "Observation"
      }
    },
    {
      "fullUrl": "urn:uuid:diagreport-1",
      "resource": {
        "resourceType": "DiagnosticReport",
        "identifier": [
          {
            "system": "https://harmonia.example/fhir/identifier/ingest-id",
            "value": "ING-20260409-0001"
          }
        ],
        "status": "final",
        "category": [
          {
            "text": "Laboratory"
          }
        ],
        "code": {
          "coding": [
            {
              "system": "https://harmonia.example/fhir/CodeSystem/report-type",
              "code": "external-lab-report",
              "display": "External Laboratory Report"
            }
          ],
          "text": "외부기관 혈액검사 결과지"
        },
        "subject": {
          "reference": "Patient/12345"
        },
        "effectiveDateTime": "2026-04-08T08:30:00+09:00",
        "issued": "2026-04-09T10:15:00+09:00",
        "performer": [
          {
            "reference": "Organization/ext-lab-01",
            "display": "외부검사기관 A"
          }
        ],
        "result": [
          {
            "reference": "urn:uuid:obs-hba1c"
          },
          {
            "reference": "urn:uuid:obs-creatinine"
          }
        ],
        "conclusion": "외부기관 반입 혈액검사 결과지에서 HbA1c 7.4%, Creatinine 1.21 mg/dL를 구조화하였으며 사용자 검토 후 확정함.",
        "presentedForm": [
          {
            "contentType": "application/pdf",
            "title": "external-lab-result-20260408.pdf",
            "url": "Binary/ext-doc-0001"
          }
        ]
      },
      "request": {
        "method": "POST",
        "url": "DiagnosticReport"
      }
    }
  ]
}
```

