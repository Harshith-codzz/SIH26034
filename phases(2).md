# SIH26034 — Development Phases

## PHASE 1: LOGIN & AUTHENTICATION

### Objective

Create secure access for registered users and establish role-based authorization.

### Tasks

- User registration
- Login / Logout
- Password reset
- Password hashing
- Authentication middleware
- Role-based authorization
- Employee role
- Manager role

### Current prototype roles

```text
Employee 1
Employee 2
Manager
```

### Access rules

Employees can inspect and generate reports.

Managers can additionally review and override rejected items.

---

## PHASE 2: DASHBOARD

### Objective

Create the main operational dashboard.

### Employee dashboard

- Scan options
- Recent inspections
- Product search
- Recent findings
- Recent reports
- Product history access

### Manager dashboard

Everything in the employee dashboard plus:

- Rejected items awaiting review
- Manager review queue
- Override actions
- Recent manager decisions

### Dashboard visualization

- Total inspections
- Accepted
- Rejected
- Manual review
- Recent inspection activity
- Product history access

Do not implement a compliance score or risk score.

---

## PHASE 3: CORE SCANNING & CRUD OPERATIONS

This is the main engineering phase.

### 3.1 Bulk Scanner

Build:

- Incoming shipment creation
- Shipment/product identification
- Quantity capture
- Batch/Lot/Code capture
- Outer package image capture
- Open/tamper detection
- Bulk inspection result

### 3.2 Counter Scanner

Build:

- Camera capture
- Upload fallback
- Image quality check
- OCR
- Computer vision
- Bounding boxes
- Structured field extraction

### 3.3 Main entities

Create/read/update operations where appropriate for:

- Users
- Products
- Shipments
- Inspections
- Extracted fields
- Findings
- Evidence
- Rules
- Manager reviews
- Reports

Deletion should be restricted for inspection/evidence records so finalized records are not casually removed.

### 3.4 Search and filtering

- Product search
- Batch/Lot search
- Inspection ID search
- Date filtering
- Scanner type filtering
- Status filtering
- Inspector filtering

---

## PHASE 4: COMPLIANCE LOGIC & ADDITIONAL FEATURES

### 4.1 Compliance Rule Engine

Implement rule groups for:

#### General

- MRP
- Required MRP wording
- Unit sale price where applicable
- Net quantity
- Manufacturer / packer / importer
- Full address
- Country of origin where applicable
- Readability/visibility

#### Food

- FSSAI logo
- FSSAI 14-digit number
- Batch/Lot/Code
- Manufactured/Packed On
- Best Before
- Use By / Expiry as applicable
- Vegetarian/non-vegetarian symbol

#### Packaging

- Open package
- Tamper indicators
- Outer package condition

---

### 4.2 Predefined Explanation Engine

Do not use RAG or LLM.

Create a mapping:

```text
Rule ID
  -> condition
  -> predefined message
```

Example:

```text
MRP_MISSING
"MRP could not be detected on the package. Manual verification is required."
```

---

### 4.3 Evidence Locker

For each finding store:

- Original image
- Cropped image
- OCR text
- Bounding box
- Rule reference
- Timestamp
- Inspector ID
- Inspection ID

---

### 4.4 Human Review

Allow employees/inspectors to:

- Review findings
- Confirm findings
- Correct findings where appropriate
- Submit inspection for finalization

---

### 4.5 Manager Review & Override

For rejected items:

```text
Rejected
  -> Manager queue
  -> Evidence review
  -> Accept / Confirm rejection
  -> Record reason
  -> Store final decision
```

The original system decision must remain visible.

---

### 4.6 Product History

Every product stores repeated inspections.

Example:

```text
Scan 1 -> 4 issues
Scan 2 -> 2 issues
Scan 3 -> 0 issues
```

---

### 4.7 Product-Wise Graph

For each product:

- X-axis = scan/inspection sequence or date
- Y-axis = number of issues

The graph is linked to product history.

---

### 4.8 Report Generation

Generate:

1. Individual product inspection reports
2. Bulk/shipment reports

Reports should include:

- Product/shipment details
- Inspection details
- Extracted fields
- Findings
- Explanations
- Evidence references
- System decision
- Manager decision
- Final status

---

### 4.9 Multilingual Support

Initial target:

- English
- Hindi
- Kannada

Additional languages can be added later.

---

### 4.10 E-commerce Scanner

Phase 2 feature:

```text
Product URL
   -> Product information / images
   -> Analysis
   -> Compliance findings
   -> Evidence
   -> Report
```

Do not let this delay the physical scanner MVP.

---

## PHASE 5: TESTING & QUALITY ASSURANCE

### Unit testing

Test:

- Rule functions
- Field extraction
- Explanation mapping
- Authorization
- Manager override logic
- Report generation
- History calculations

### Integration testing

Test:

```text
Scanner
 -> OCR/CV
 -> Extraction
 -> Rules
 -> Findings
 -> Evidence
 -> Review
 -> Report
 -> History
```

### Computer vision testing

Use representative:

- Clear packages
- Blurry packages
- Glare
- Different orientations
- Damaged/open cartons
- Different label layouts
- Multiple languages
- Food/non-food packages

### Permission testing

Ensure:

- Unregistered users cannot access
- Employees cannot override manager decisions
- Managers can review rejections
- Finalized records are protected

### Performance testing

Measure:

- Scan processing time
- OCR processing time
- Database response
- Concurrent scans
- Report generation time

---

## PHASE 6: DEPLOYMENT & MAINTENANCE

### Deployment

Deploy:

- Frontend
- Backend API
- PostgreSQL
- File storage
- Rule repository

### Production considerations

- Environment variables
- Database backups
- Access control
- Logging
- Error handling
- Secure file handling

### Maintenance

- Update rule definitions when requirements change
- Improve OCR/CV performance
- Fix bugs
- Monitor scan failures
- Maintain report/history integrity

### Future enhancements

- More Indian languages
- More product categories
- E-commerce scanner expansion
- Better camera hardware integration
- Additional reporting views

Do not add previously removed features unless the project scope is deliberately revised.
