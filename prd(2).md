# SIH26034 — Product Requirements Document (PRD)

## 1. WHAT TO BUILD

### Product name

**Packaged Commodity Compliance Scanner**

### Product type

AI-assisted packaged commodity inspection and compliance platform.

### Core purpose

Build a system that checks packaged commodities at two important points:

1. **When large quantities arrive** from factories, warehouses, suppliers, or distributors.
2. **When an individual product is sold** at the counter.

The system should detect package condition and required declarations using computer vision and OCR, evaluate them using a deterministic compliance rule engine, provide predefined explanations for findings, preserve evidence, support human review, allow manager override, maintain product history and graphs, and generate inspection reports.

### Core workflow

```text
SCAN
  -> UNDERSTAND
  -> CHECK
  -> HIGHLIGHT
  -> EXPLAIN
  -> REVIEW
  -> DECIDE
  -> REPORT
  -> STORE HISTORY
```

### What this product is not

It is not:

- A generic OCR scanner
- A chatbot
- An LLM-based legal advisor
- A risk-scoring system
- A fake-product detector
- A predictive analytics platform

---

## 2. TARGETED USERS

### 2.1 Employee / Inspector

The employee uses the system to:

- Inspect incoming products
- Scan individual products
- Review detected information
- Review findings
- View evidence
- Generate reports
- View permitted product history

### 2.2 Manager

The manager can:

- Perform inspections
- Review employee inspections
- Review rejected products
- View evidence
- Correct/confirm findings
- Accept a rejected item when appropriate
- Confirm rejection
- Finalize the decision

### Current prototype account structure

```text
Employee 1
Employee 2
Manager
```

Only registered users can access the application.

---

## 3. FEATURES

## 3.1 Dual Scanner System

### A. Bulk / Incoming Goods Scanner

Used for large consignments.

Example:

```text
Large carton
Contains 100 packets
      |
      v
Bulk scanner
```

Checks:

- Outer packaging
- Seal condition
- Open/tampered packaging
- Quantity
- Batch/Lot/Code
- Applicable outer-package information

### Core rule

```text
Open / Tampered
      -> Reject / Quarantine
```

A manager can review and override the rejection.

---

### B. Counter / Individual Product Scanner

Used for products at the point of sale.

Checks the full package using:

- Camera
- Image processing
- OCR
- Computer vision
- Compliance rules

---

## 3.2 Smart Camera / Image Quality

Before analysis, verify:

- Blur
- Glare
- Orientation
- Perspective
- Brightness
- Text visibility

Poor image:

```text
Ask user to recapture
```

Good image:

```text
Proceed to analysis
```

---

## 3.3 OCR & Computer Vision

### OCR

Extract:

- MRP
- Unit sale price where applicable
- Net quantity
- Manufacturer / packer / importer
- Address
- Country of origin
- FSSAI number
- Batch/Lot/Code
- Date information

### Computer Vision

Detect:

- Open/tampered packaging
- FSSAI logo
- Vegetarian/non-vegetarian symbol
- Text regions
- Bounding boxes
- Readability/visibility

---

## 3.4 Compliance Rule Engine

### General checks

- MRP
- Required MRP wording
- Unit sale price where applicable
- Net quantity
- Manufacturer / packer / importer
- Full address
- Country of origin where applicable
- Readability / visibility

### Food-specific checks

- FSSAI logo
- FSSAI 14-digit licence/registration number
- Batch/Lot/Code
- Date of Manufacture / Packed On
- Best Before
- Use By / Expiry as applicable
- Vegetarian/non-vegetarian symbol

### Output states

```text
PASS
FINDING
UNCERTAIN / MANUAL REVIEW
```

---

## 3.5 Predefined Explanation System

The project will not use RAG or LLM.

Each finding is linked to a predefined sentence.

Example:

```text
Finding:
FSSAI number not detected

Explanation:
"FSSAI licence/registration number could not be detected
on the package. Manual verification is required."
```

Benefits:

- Consistent output
- Predictable behavior
- Easier testing
- No API dependency
- No generative hallucination

---

## 3.6 Evidence Locker

Every finding keeps evidence.

Stored evidence:

- Original image
- Zoomed crop
- OCR text
- Bounding box
- Rule reference
- Timestamp
- Inspector ID
- Inspection ID

The evidence should be accessible directly from the inspection result.

---

## 3.7 Human-in-the-Loop Review

The system does not assume that every automated finding is final.

The employee/inspector can:

- Confirm finding
- Correct finding
- Request review
- Finalize their inspection stage

This is particularly useful when OCR or image analysis is uncertain.

---

## 3.8 Manager Override

### Example

```text
System:
REJECT

Reason:
Packaging appears open

        |
        v

Manager Review

        |
   +----+----+
   |         |
 ACCEPT    REJECT
 Override   Confirm
```

If the manager accepts the item, the final record should preserve:

- System decision
- Manager decision
- Manager ID
- Reason
- Timestamp

---

## 3.9 Product Compliance History

Each product has a persistent history.

Example:

```text
ABC Rice 5kg

Scan #1 -> 4 issues
Scan #2 -> 2 issues
Scan #3 -> 0 issues
```

History includes:

- Scan date/time
- Inspector
- Scanner type
- Batch/Lot
- Findings
- Evidence
- System decision
- Manager decision
- Final status

---

## 3.10 Product-Wise Graph

Every product has an issue-history graph.

Example:

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

The graph shows the number of recorded issues for each scan.

---

## 3.11 Reports

### Individual product report

Every product inspection can generate a detailed report containing:

- Inspection ID
- Product details
- Batch/Lot
- Scanner type
- Inspector
- Date/time
- Location
- Extracted information
- Compliance checks
- Findings
- Predefined explanations
- Evidence
- System result
- Manager result
- Final result

### Bulk/shipment report

A shipment report summarizes:

- Shipment ID
- Supplier
- Total items/quantity
- Accepted
- Rejected
- Manual review
- Product-wise results

---

## 3.12 Inspection History

The application should support:

- Product search
- Batch/Lot search
- Inspection ID search
- Date filters
- Status filters
- Scanner type filters
- Inspector filters

The history should preserve the inspection trail.

---

## 3.13 Multilingual Support

Initial target:

- English
- Hindi
- Kannada

The same compliance rules are applied after language normalization.

---

## 3.14 E-commerce Scanner

Phase 2.

User supplies a product URL.

```text
URL
 -> Product information
 -> Product images
 -> OCR / CV analysis
 -> Compliance engine
 -> Findings
 -> Evidence
 -> Report
```

---

## 4. FUNCTIONAL REQUIREMENTS

### Authentication

- Only registered users can log in.
- Users must be assigned a role.
- Role permissions must be enforced at API and UI levels.

### Bulk scanning

- Create incoming shipment
- Scan outer package
- Detect open/tampered package
- Capture batch/lot and quantity
- Produce inspection result

### Counter scanning

- Capture or upload package image
- Validate image quality
- Run OCR
- Run computer vision
- Extract structured fields
- Run compliance rules
- Show findings and evidence

### Review

- Allow human confirmation/correction
- Send rejected items to manager review where required
- Record final manager decision

### Reporting

- Generate report for each inspection
- Generate consolidated shipment report
- Keep generated reports accessible from history

### History

- Store inspections permanently
- Display product history
- Display product-wise graphs

---

## 5. NON-FUNCTIONAL REQUIREMENTS

### Security

- Secure authentication
- Password hashing
- Role-based authorization
- Controlled file access
- Protected inspection records

### Reliability

- Preserve evidence
- Preserve original system decisions
- Preserve manager decisions
- Handle failed OCR gracefully
- Require recapture for unusable images

### Maintainability

- Keep rules in a structured rule repository
- Keep explanation messages separate from UI code
- Separate scanner, OCR, CV, rules, evidence, reports, and history services

### Performance

The system should provide practical response times for normal inspection workflows and should be designed so bulk processing can be improved independently of the counter scanner.

---

## 6. MAIN USER FLOWS

### Flow A — Incoming shipment

```text
Login
  -> Bulk Scanner
  -> Capture carton
  -> Image / package analysis
  -> Seal/open check
  -> Batch / quantity information
  -> Compliance checks
  -> PASS or REJECT
  -> Evidence
  -> Human review
  -> Manager review if rejected
  -> Final decision
  -> Shipment report
  -> History
```

### Flow B — Individual counter item

```text
Login
  -> Counter Scanner
  -> Capture product
  -> Image quality check
  -> OCR + CV
  -> Field extraction
  -> Compliance rule engine
  -> Findings
  -> Evidence
  -> Predefined explanation
  -> Human review
  -> Final result
  -> Product report
  -> Product history
  -> Product graph
```

### Flow C — Manager override

```text
System rejection
  -> Manager queue
  -> Evidence review
  -> Manager decision
  -> Record reason
  -> Final status
```

---

## 7. SUCCESS CRITERIA

The MVP is successful when it can demonstrate:

1. Registered users can log in according to their roles.
2. Employees can perform both scanner workflows.
3. The bulk scanner can detect an open/tampered package case.
4. The counter scanner can extract selected package declarations.
5. The rule engine can identify supported compliance findings.
6. Each finding can show evidence.
7. Predefined explanations are shown for supported findings.
8. Employees can review results.
9. Managers can accept or confirm rejected items.
10. Reports can be generated for inspected products and shipments.
11. Product history is stored.
12. Every product can display an issue-vs-scan graph.

---

## 8. OUT OF SCOPE

The following are intentionally excluded:

- RAG
- LLM
- AI chatbot
- Generative explanations
- Compliance score
- Risk score
- Before/after compliance comparison
- Duplicate/fake package detection
- Predictive analytics
- Evidence hashing

The e-commerce scanner is planned as a later phase rather than a core MVP requirement.
