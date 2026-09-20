# SIH26034 — Project Memory

## 1. MEMORY

This file stores the important project decisions, current scope, progress direction, and constraints for SIH26034 — Packaged Commodity Compliance Scanner.

### Project identity

The final project is an AI-assisted packaged commodity inspection and compliance system with:

- A bulk/incoming goods scanner
- A counter/individual product scanner
- OCR and computer vision
- A deterministic compliance rule engine
- Predefined explanation messages
- Evidence storage
- Human review
- Manager approval/override
- Product compliance history
- Product-wise issue graphs
- Report generation
- Role-based authentication

### Important exclusions

The project does **not** use:

- RAG
- LLM
- AI chatbot
- AI-generated explanations
- Compliance score
- Risk score
- Before/after compliance comparison
- Duplicate/fake package detection
- Predictive analytics
- Evidence hashing

---

## 2. WHAT HAPPENED

### Scope decisions

The project originally contained a larger set of possible features. After review, the scope was narrowed to the features that directly support the inspection workflow.

### Selected requirements

- Bulk/incoming scanner
- Counter scanner
- Compliance vision
- Explainable findings
- Smart camera/image quality checks
- E-commerce scanner as later phase
- Product compliance history
- Multilingual support
- Evidence locker
- Product-wise graph
- Reports for products and shipments
- Human-in-the-loop review
- Employee/manager roles
- Manager override

### New workflow decision

There are two scanners because incoming large consignments and individual sale items have different inspection needs.

### Explanation decision

The system uses predefined sentences tied to specific rule/finding cases instead of generative AI.

### Governance decision

Employees can inspect and review, while the manager can review rejected items and override a system rejection when necessary.

---

## 3. CURRENTLY WORKING ON

The current implementation plan is centered on:

1. Finalizing the architecture
2. Defining the PRD
3. Defining development phases
4. Designing the database model
5. Designing the rule structure
6. Designing the dual-scanner workflow
7. Defining evidence and report structures
8. Defining role-based permissions
9. Defining product history and graphs
10. Preparing the MVP development plan

### Primary MVP

The first working version should prioritize:

```text
Authentication
  -> Bulk scanner
  -> Counter scanner
  -> Image processing
  -> OCR + CV
  -> Field extraction
  -> Compliance rule engine
  -> Predefined explanations
  -> Evidence
  -> Human review
  -> Manager override
  -> Reports
  -> Product history
  -> Product graphs
```

---

## 4. UPDATES

### Current architecture direction

The architecture is now:

```text
User
  -> Scanner
  -> Image Processing
  -> OCR + Computer Vision
  -> Structured Extraction
  -> Compliance Rule Engine
  -> Findings
  -> Evidence
  -> Predefined Explanation
  -> Human Review
  -> Manager Review when required
  -> Final Decision
  -> Report / History / Graph
```

### User roles

Current prototype:

- Employee 1
- Employee 2
- Manager

Only registered users can join/access the system.

### Compliance areas

General:

- MRP
- MRP wording
- Unit sale price where applicable
- Net quantity
- Manufacturer / packer / importer
- Full address
- Country of origin where applicable
- Readability/visibility

Food:

- FSSAI logo
- 14-digit FSSAI number
- Batch/Lot/Code
- Manufactured/Packed On
- Best Before / Use By / Expiry as applicable
- Vegetarian/non-vegetarian symbol

### Data and history

Each inspection stores timestamps, product information, evidence, findings, decisions, and history. Product-wise graphs show issue counts across scans.

### Reports

Reports are required for all inspected products, with consolidated shipment reports for bulk intake.

---

## 5. PURPOSE

### Project purpose

Build a practical inspection-support system that reduces the manual effort involved in checking packaged commodity labels and package condition.

### Design purpose

The system should:

- Help inspectors inspect quickly
- Make findings understandable
- Preserve evidence
- Keep final decisions reviewable
- Allow manager control over exceptions
- Maintain historical records
- Generate inspection-ready reports
- Keep compliance logic deterministic and maintainable

### Development purpose

Keep the implementation realistic for a 3rd-semester engineering team by avoiding unnecessary AI complexity and focusing on a strong, demonstrable core workflow.

---

## 6. Permanent Decisions

These decisions should not be changed without an explicit project-scope discussion:

- No RAG/LLM
- No generative explanation
- Use predefined explanation sentences
- Two scanner architecture
- Human review remains part of the workflow
- Manager override remains part of the workflow
- Product-wise graphs are required
- Reports for all scanned products are required
- Evidence locker is required
- Product history is required
- Evidence hashing is removed
- E-commerce scanning is Phase 2, not MVP
