# Patient Data Models Schema

Complete reference for the medical record data models.

## Structure Overview

```
Patient
├── Demographics (patient_id, name, DOB, gender)
├── ContactInfo (phone, address, emergency contact)
├── Insurance (provider, plan type)
├── Allergy[] (allergen, reaction, status)
├── Medication[] (name, dosage, frequency)
├── Diagnosis[] (condition, ICD code, status)
├── ClinicalNote[] (SOAP note with vitals)
├── LabResult[] (test name, result, reference range)
│   ├── CompleteBloodCount
│   ├── LipidPanel
│   └── ChemistryPanel
├── ImagingStudy[] (type, findings, impression)
└── DiagnosticTest[] (type, findings, interpretation)
```

---

## Enums

### Gender
`MALE` | `FEMALE` | `OTHER`

### AllergyStatus
`CONFIRMED` | `SUSPECTED` | `DENIED`

---

## Models

### ContactInfo
| Field | Type | Required |
|---|---|---|
| phone | str | yes |
| address | str | yes |
| emergency_contact_name | str | yes |
| emergency_contact_relation | str | yes |
| emergency_contact_phone | str | no |

### Insurance
| Field | Type | Required |
|---|---|---|
| provider | str | yes |
| plan_type | str | yes |

### Allergy
| Field | Type | Required | Default |
|---|---|---|---|
| allergen | str | yes | |
| reaction | str | no | None |
| status | AllergyStatus | no | CONFIRMED |
| recorded_at | datetime | no | None |

### Medication
| Field | Type | Required |
|---|---|---|
| name | str | yes |
| dosage | str | yes |
| frequency | str | yes |
| indication | str | no |
| prescribed_at | datetime | no |

### VitalSigns
All fields optional.

| Field | Type |
|---|---|
| blood_pressure_systolic | int |
| blood_pressure_diastolic | int |
| heart_rate | int |
| temperature_fahrenheit | float |
| bmi | float |
| measurement_date | datetime |

### ClinicalNote
Uses SOAP format (Subjective, Objective, Assessment, Plan).

| Field | Type | Required |
|---|---|---|
| note_type | str | yes |
| date_of_service | date | yes |
| provider_name | str | yes |
| provider_credentials | str | yes |
| subjective | str | yes |
| objective | str | yes |
| assessment | str | yes |
| plan | str | yes |
| chief_complaint | str | no |
| vital_signs | VitalSigns | no |
| follow_up_instructions | str | no |

### Diagnosis
| Field | Type | Required | Default |
|---|---|---|---|
| condition | str | yes | |
| icd_code | str | no | None |
| date_diagnosed | date | no | None |
| status | str | no | "active" |

Status values: `active`, `resolved`, `chronic`.

### LabResult
Base class for all lab results.

| Field | Type | Required | Default |
|---|---|---|---|
| test_name | str | yes | |
| result | str | yes | |
| unit | str | yes | |
| reference_range | str | yes | |
| flagged | bool | no | False |
| date_performed | date | no | None |

#### CompleteBloodCount (extends LabResult)
| Field | Type |
|---|---|
| wbc | float |
| rbc | float |
| hemoglobin | float |
| hematocrit | float |
| platelets | float |

#### LipidPanel (extends LabResult)
| Field | Type |
|---|---|
| total_cholesterol | int |
| ldl_cholesterol | int |
| hdl_cholesterol | int |
| triglycerides | int |

#### ChemistryPanel (extends LabResult)
| Field | Type |
|---|---|
| sodium | float |
| potassium | float |
| co2 | int |
| creatinine | float |
| ast | int |
| total_bilirubin | float |

### ImagingStudy
| Field | Type | Required |
|---|---|---|
| study_type | str | yes |
| body_part | str | yes |
| findings | str | yes |
| impression | str | yes |
| date_performed | date | yes |
| radiologist | str | no |

### DiagnosticTest
| Field | Type | Required |
|---|---|---|
| test_type | str | yes |
| date_performed | date | yes |
| findings | str | yes |
| interpretation | str | yes |
| ordered_by | str | no |

---

## Patient (root model)

| Field | Type | Required | Default |
|---|---|---|---|
| patient_id | str | yes | |
| medical_record_number | str | yes | |
| first_name | str | yes | |
| last_name | str | yes | |
| date_of_birth | date | yes | |
| gender | Gender | yes | |
| contact_info | ContactInfo | yes | |
| insurance | Insurance | yes | |
| allergies | List[Allergy] | no | [] |
| current_medications | List[Medication] | no | [] |
| diagnoses | List[Diagnosis] | no | [] |
| clinical_notes | List[ClinicalNote] | no | [] |
| lab_results | List[LabResult] | no | [] |
| imaging_studies | List[ImagingStudy] | no | [] |
| diagnostic_tests | List[DiagnosticTest] | no | [] |
| primary_care_physician | str | no | None |
| hospital | str | no | None |
| admission_date | date | no | None |
| patient_signature | str | no | None |
| signature_date | datetime | no | None |

### Helper methods
- `age()` — calculates current age from date_of_birth
- `get_active_diagnoses()` — returns diagnoses with status "active"
- `get_recent_labs(days=30)` — returns lab results from the last N days
