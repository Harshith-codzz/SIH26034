# SIH26034 — Architecture

## 1. Overview

SIH26034 is an AI-assisted packaged commodity inspection and compliance system with two operational scanners:

1. **Bulk / Incoming Goods Scanner** — used when large consignments arrive from a factory, warehouse, supplier, or distributor.
2. **Counter / Individual Product Scanner** — used when an individual packaged product is presented at the sales counter.

The system combines computer vision, OCR, structured field extraction, a deterministic compliance rule engine, predefined explanation messages, evidence capture, human review, manager approval/override, product history, product-wise graphs, and report generation.

There is **no RAG, LLM, AI chatbot, AI-generated explanation, risk score, compliance score, duplicate/fake detection, before/after module, or evidence hashing** in the final scope.

---

## 2. High-Level Architecture

```text
                         REGISTERED USERS
                               |
                    +----------+----------+
                    |                     |
                 EMPLOYEE              MANAGER
                 User 1 / 2               |
                    |                     |
                    +----------+----------+
                               |
                       INSPECTION SYSTEM
                               |
                +--------------+---------------+
                |                              |
                v                              v
         +---------------+              +---------------+
         | BULK SCANNER  |              | COUNTER       |
         | Incoming      |              | SCANNER       |
         | consignments  |              | Individual    |
         +-------+-------+              +-------+-------+
                 |                              |
                 |                              |
          Package / seal                  Image quality
          / tamper check                  + OCR + CV
          Batch / Lot                     label analysis
          Quantity                              |
                 |                              |
                 +---------------+--------------+
                                 |
                                 v
                       IMAGE PROCESSING
                                 |
                                 v
                         OCR + COMPUTER VISION
                                 |
                                 v
                       FIELD EXTRACTION
                                 |
                                 v
                       COMPLIANCE RULE ENGINE
                                 |
                  +--------------+--------------+
                  |                             |
                  v                             v
                 PASS                         FINDING
                                                |
                                                v
                                        EVIDENCE ENGINE
                                                |
                                                v
                                   PREDEFINED EXPLANATION
                                                |
                                                v
                                         HUMAN REVIEW
                                                |
                                  +-------------+-------------+
                                  |                           |
                                  v                           v
                                ACCEPT                      REJECT
                                                              |
                                                              v
                                                       MANAGER REVIEW
                                                              |
                                                     +--------+--------+
                                                     |                 |
                                                     v                 v
                                                  ACCEPT             REJECT
                                                 override            confirm
                                                     |                 |
                                                     +--------+--------+
                                                              |
                                                              v
                                                        FINAL DECISION
                                                              |
                            +-------------------+-------------+----------------+
                            |                   |                              |
                            v                   v                              v
                         REPORT              EVIDENCE                       HISTORY
                                                                                 |
                                                                                 v
                                                                            PRODUCT GRAPH
                                                                                 |
                                                                                 v
                                                                            DATABASE
```

---

## 3. Scanner 1 — Bulk / Incoming Goods Scanner

### Purpose

Inspect large incoming consignments before they enter inventory.

### Example

A large carton arrives containing 100 packets of chips.

### Flow

```text
Incoming carton
    -> Bulk scanner
    -> Outer packaging detection
    -> Seal/open/tamper detection
    -> Batch / Lot / Code
    -> Quantity / shipment information
    -> Applicable compliance checks
    -> System decision
```

### Key checks

- Product / shipment identification
- Quantity
- Batch, Lot, or Code
- Outer packaging condition
- Open package detection
- Visible tampering or damage indicators
- Required outer-package information where applicable

### Packaging rule

```text
SEALED
  -> Continue inspection

OPEN / TAMPERED
  -> Reject / Quarantine
  -> Save evidence
  -> Send to manager review when required
```

A closed bulk carton is not expected to expose every inner packet. Detailed individual-package declarations are handled by the counter scanner.

---

## 4. Scanner 2 — Counter / Individual Product Scanner

### Purpose

Perform detailed inspection of the individual product before sale.

### Flow

```text
Individual package
    -> Camera capture
    -> Image quality check
    -> OCR + Computer Vision
    -> Field extraction
    -> Compliance rule engine
    -> Findings / Pass
    -> Evidence
    -> Human review
    -> Final decision
```

---

## 5. Image Processing Layer

Before OCR and detailed analysis:

- Blur detection
- Glare detection
- Orientation check
- Perspective correction
- Brightness / visibility validation
- Image enhancement

Example:

```text
Poor image
   -> Recapture

Acceptable image
   -> OCR + Computer Vision
```

---

## 6. OCR + Computer Vision

### OCR responsibilities

Extract visible text from the package, such as:

- MRP
- Unit sale price where applicable
- Net quantity
- Manufacturer / packer / importer
- Address
- Country of origin where applicable
- FSSAI number
- Batch / Lot / Code
- Date information

### Computer Vision responsibilities

Detect or inspect visual elements such as:

- Open/tampered packaging
- FSSAI logo
- Vegetarian/non-vegetarian symbol
- Text regions
- Bounding boxes
- Visibility/readability

---

## 7. Structured Field Extraction

Raw OCR text is converted into structured fields.

Example:

```json
{
  "mrp": "₹50",
  "unit_sale_price": "₹25/100g",
  "net_quantity": "200g",
  "manufacturer": "ABC Foods Pvt Ltd",
  "manufacturer_address": "Example Address",
  "country_of_origin": "India",
  "fssai_number": "12345678901234",
  "batch_number": "B24091",
  "packed_on": "08/2026",
  "best_before": "6 months"
}
```

Each extracted field may also store:

- OCR confidence
- Bounding box
- Source image reference

---

## 8. Compliance Rule Engine

The rule engine is the main compliance-checking component.

```text
Extracted fields
     |
Applicable rule set
     |
Validation
     |
+---------+------------+
|         |            |
PASS    FINDING    UNCERTAIN
```

The application should use structured/versioned rules rather than placing all compliance logic directly inside individual UI components.

### Rule categories

- MRP and required wording
- Unit sale price where applicable
- Net quantity
- Manufacturer / packer / importer details
- Address information
- Country of origin where applicable
- Readability / visibility
- Food-specific FSSAI checks
- Batch / Lot / Code
- Date marking
- Vegetarian / non-vegetarian symbol
- Packaging open/tamper condition

---

## 9. Predefined Explanation Engine

No RAG and no LLM are used.

Each rule/finding maps to a predefined sentence.

```text
Rule ID
   -> Detected case
   -> Predefined sentence
   -> Display to inspector
```

Example:

```text
Rule: FSSAI_01
Condition: FSSAI number not detected

Message:
"FSSAI licence/registration number could not be detected
on the package. Manual verification is required."
```

The same principle is used for packaging, manufacturer details, dates, quantity, and other supported cases.

---

## 10. Evidence Engine

Every finding should have supporting evidence.

### Evidence package

- Original image
- Zoomed/cropped region
- OCR text
- Bounding box
- Rule ID / requirement
- Timestamp
- Inspector ID
- Inspection ID

Example:

```text
Finding
 |
 +-- Original image
 +-- Zoomed crop
 +-- OCR text
 +-- Bounding box
 +-- Rule reference
 +-- Timestamp
 +-- Inspector
```

---

## 11. Human-in-the-Loop Review

AI and rule-engine findings are not blindly treated as the final decision.

```text
System finding
    -> Human review
    -> Confirm / Correct
    -> Continue to final decision
```

This is especially important for uncertain OCR, partially visible text, image-quality problems, and ambiguous packaging conditions.

---

## 12. Roles and Authorization

### Employees

Current prototype users:

- Employee 1
- Employee 2

Permissions:

- Login
- Scan products
- Perform inspections
- View findings
- View evidence
- Generate reports
- View permitted history

Employees cannot override a manager decision.

### Manager

Permissions:

- All employee capabilities
- Review rejected items
- Review evidence
- Review/correct findings
- Accept a rejected item
- Confirm rejection
- Finalize the decision

---

## 13. Manager Override Flow

Example:

```text
Bulk scanner
    -> Packaging appears open
    -> System: REJECT
    -> Evidence saved
    -> Manager review
    -> Manager ACCEPT or CONFIRM REJECTION
```

If the manager accepts:

```text
System decision: REJECT
Manager decision: ACCEPT
Final status: ACCEPTED BY MANAGER
```

The original system decision remains in the audit trail.

---

## 14. Product Compliance History

Every scanned product receives a persistent inspection history.

Example:

```text
ABC Rice 5kg

Scan #1
12 Sep 2026
Issues: 4

Scan #2
15 Sep 2026
Issues: 2

Scan #3
18 Sep 2026
Issues: 0
```

Each inspection can contain:

- Product
- Batch / Lot
- Scanner type
- Inspector
- Timestamp
- Location
- Extracted fields
- Findings
- Evidence
- System decision
- Manager decision
- Final decision

---

## 15. Product-Wise Graph

Each product gets its own history graph.

### Graph

```text
Issues
5 |
4 | ●
3 |
2 |       ●
1 |
0 |              ●
  +----------------------
    S1      S2      S3
```

The graph represents the number of recorded issues across scans.

The product history page should show:

- Product name
- Total scans
- Latest result
- Current issues
- Historical scans
- Batch history
- Issues-vs-scan graph

---

## 16. Report Generation

Reports are generated for all inspections.

### Individual product report

```text
Inspection ID
Product
Category
Batch / Lot
Scanner type
Inspector
Date / Time
Location

Extracted information

Compliance checks

Findings

Predefined explanations

Evidence

System decision
Manager decision
Final decision
```

### Bulk / shipment report

For a large shipment:

```text
Shipment ID
Supplier
Total quantity
Accepted
Rejected
Manual review
Product-wise inspection results
```

The shipment report can reference detailed individual product reports.

---

## 17. Multilingual Support

Initial implementation can focus on:

- English
- Hindi
- Kannada

Architecture:

```text
Package
  -> Language detection
  -> Multilingual OCR
  -> Normalized fields
  -> Same compliance rule engine
```

The compliance logic remains language-independent.

---

## 18. E-commerce Scanner

This remains a Phase 2 feature.

```text
Product URL
   -> Product information / images
   -> OCR / visual analysis
   -> Compliance engine
   -> Findings + evidence
   -> Report
```

It should not block the physical scanning MVP.

---

## 19. Recommended Technology Stack

### Frontend

- React
- Tailwind CSS

### Backend

- Python
- FastAPI

### Computer Vision

- OpenCV

### OCR

- PaddleOCR

### Database

- PostgreSQL

### Report generation

- Python-based PDF/reporting library

### Storage

- Local/server storage for the prototype
- Cloud object storage later if required

---

## 20. Suggested Folder & File Structure

```text
sih26034/
|
├── frontend/
│   ├── src/
│   │   ├── components/
│   │   ├── pages/
│   │   ├── services/
│   │   └── utils/
│   └── package.json
|
├── backend/
│   ├── app/
│   │   ├── api/
│   │   ├── auth/
│   │   ├── scanners/
│   │   │   ├── bulk/
│   │   │   └── counter/
│   │   ├── vision/
│   │   ├── ocr/
│   │   ├── extraction/
│   │   ├── rules/
│   │   ├── explanations/
│   │   ├── evidence/
│   │   ├── inspections/
│   │   ├── reviews/
│   │   ├── reports/
│   │   ├── history/
│   │   └── database/
│   └── requirements.txt
|
├── rules/
│   ├── general/
│   ├── food/
│   └── rule_versions/
|
├── storage/
│   ├── originals/
│   ├── crops/
│   └── reports/
|
├── tests/
│   ├── unit/
│   ├── integration/
│   └── vision/
|
├── docs/
│   ├── architecture.md
│   ├── prd.md
│   ├── phases.md
│   └── memory.md
|
└── README.md
```

---

## 21. Architecture Principles

1. **AI for perception** — OCR and computer vision detect text/visual information.
2. **Rules for compliance** — deterministic rule engine evaluates applicable requirements.
3. **Predefined text for explanations** — no LLM.
4. **Human review for uncertainty** — inspectors can confirm or correct findings.
5. **Manager authority for overrides** — rejected items can be reviewed and accepted by the manager.
6. **Evidence-first inspection** — each finding retains supporting evidence.
7. **History by product** — repeated scans update product history and graphs.
8. **Reports are first-class outputs** — every inspection can produce a report.
9. **Role-based access** — employees and managers have different permissions.
10. **MVP-first development** — physical inspection is implemented before Phase 2 e-commerce functionality.
