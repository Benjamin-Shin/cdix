# FHIR 예시 시나리오 (Bundle)

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

> Patient/12345, Organization/ext-lab-01, Organization/hospital-01, Practitioner/prv-1001는 이미 FHIR 서버에 존재하고, 아래 attachment.data와 presentedForm.data는 예시용 축약 Base64다. 실제 운영에서는 이 부분을 실데이터로 바꾸거나, 문서 원본을 별도 저장소/Binary로 두고 URL 참조로 바꾸면 된다. Provenance는 구조화 결과 생성/승인 활동을 기록하도록 DiagnosticReport와 Observation들을 target으로 잡고, 원본 문서는 entity.role = source로 연결했다.

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
        "authenticator": {
          "reference": "Practitioner/prv-1001",
          "display": "홍길동"
        },
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
              "data": "JVBERi0xLjQKMSAwIG9iago8PAovVHlwZSAvQ2F0YWxvZwo+PgplbmRvYmoK"
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
        "resultsInterpreter": [
          {
            "reference": "Practitioner/prv-1001",
            "display": "홍길동"
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
            "data": "JVBERi0xLjQKMSAwIG9iago8PAovVHlwZSAvQ2F0YWxvZwo+PgplbmRvYmoK"
          }
        ]
      },
      "request": {
        "method": "POST",
        "url": "DiagnosticReport"
      }
    },
    {
      "fullUrl": "urn:uuid:provenance-1",
      "resource": {
        "resourceType": "Provenance",
        "target": [
          {
            "reference": "urn:uuid:diagreport-1"
          },
          {
            "reference": "urn:uuid:obs-hba1c"
          },
          {
            "reference": "urn:uuid:obs-creatinine"
          }
        ],
        "occurredDateTime": "2026-04-09T10:05:00+09:00",
        "recorded": "2026-04-09T10:15:00+09:00",
        "activity": {
          "coding": [
            {
              "system": "https://harmonia.example/fhir/CodeSystem/provenance-activity",
              "code": "review-approve-extract",
              "display": "Review, approve, and commit extracted results"
            }
          ],
          "text": "사용자 검토 후 구조화 결과 확정"
        },
        "agent": [
          {
            "who": {
              "reference": "Practitioner/prv-1001",
              "display": "홍길동"
            },
            "onBehalfOf": {
              "reference": "Organization/hospital-01",
              "display": "도입병원"
            }
          },
          {
            "who": {
              "reference": "Organization/hospital-01",
              "display": "Harmonia CDIX"
            }
          }
        ],
        "entity": [
          {
            "role": "source",
            "what": {
              "reference": "urn:uuid:docref-1"
            }
          }
        ]
      },
      "request": {
        "method": "POST",
        "url": "Provenance"
      }
    },
    {
      "fullUrl": "urn:uuid:auditevent-1",
      "resource": {
        "resourceType": "AuditEvent",
        "type": {
          "system": "https://harmonia.example/fhir/CodeSystem/audit-event-type",
          "code": "document-approval-commit",
          "display": "Document approval and FHIR commit"
        },
        "subtype": [
          {
            "system": "https://harmonia.example/fhir/CodeSystem/audit-event-subtype",
            "code": "external-result-ingest",
            "display": "External result document ingestion"
          }
        ],
        "action": "C",
        "recorded": "2026-04-09T10:15:00+09:00",
        "outcome": "0",
        "outcomeDesc": "CDIX reviewed results committed successfully to FHIR server.",
        "agent": [
          {
            "who": {
              "reference": "Practitioner/prv-1001",
              "display": "홍길동"
            },
            "requestor": true,
            "name": "홍길동"
          }
        ],
        "source": {
          "site": "CDIX",
          "observer": {
            "reference": "Organization/hospital-01",
            "display": "Harmonia CDIX"
          }
        },
        "entity": [
          {
            "what": {
              "reference": "urn:uuid:docref-1"
            },
            "name": "Original external result document",
            "description": "Source PDF document indexed as DocumentReference"
          },
          {
            "what": {
              "reference": "urn:uuid:diagreport-1"
            },
            "name": "Structured diagnostic report",
            "description": "Final DiagnosticReport committed after review"
          },
          {
            "what": {
              "reference": "urn:uuid:obs-hba1c"
            },
            "name": "HbA1c result",
            "description": "Structured Observation committed after review"
          },
          {
            "what": {
              "reference": "urn:uuid:obs-creatinine"
            },
            "name": "Creatinine result",
            "description": "Structured Observation committed after review"
          }
        ]
      },
      "request": {
        "method": "POST",
        "url": "AuditEvent"
      }
    }
  ]
}
```

### 이 예시에서 중요한 포인트

> Provenance.target은 최종 확정된 구조화 결과에만 걸고, 원본 결과지는 entity.role = source로 연결했다. 그래서 “무엇을 근거로 어떤 FHIR 결과가 만들어졌는지”가 또렷하다. Provenance.entity.role은 derivation | revision | quotation | source | removal 값을 쓰고, agent는 1개 이상이 필요하다.

> AuditEvent는 저장 행위 자체를 감사하는 용도로 잡았다. type은 필수이고, recorded는 필수이며, source.observer도 필수다. entity.what에는 어떤 리소스가 이벤트에 관련됐는지 넣을 수 있다.

> DiagnosticReport.result는 Observation을 참조하고, presentedForm은 “발행된 전체 보고서”를 담는 자리라서 외부 결과지 원본을 같이 보관하기에 잘 맞는다.

다음으로 바로 이어가면 좋은 건 두 가지야.

하나는 이 JSON을 HAPI FHIR 서버 기준 저장 플로우로 바꾸는 것, 다른 하나는 CDIX 내부 운영 DB ↔ FHIR 리소스 매핑표를 만드는 것.

