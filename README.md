# Systematic Review Integrity Checker

**Tools:** Python, Gemini 2.5, Qwen-Plus, DeepSeek R1, PubMed API, CrossRef API, PyMuPDF  
**Domain:** Healthcare AI / Academic Integrity / NLP  
**Type:** MSc Group Project - University of Liverpool (COMP530)  
**Team:** Team 18 (8 members)

---

## Project Overview

Manual verification of a systematic review takes educators 3-5 hours per submission - checking search reproducibility, reference validity, extracted data accuracy, and table consistency. This project delivers an **LLM-assisted, decision-support tool** that automates this process in under 5 minutes.

The system accepts a student's systematic review as a PDF or structured YAML file, runs it through a 4-stage pipeline, and produces a human-readable DOCX report flagging discrepancies without replacing human judgement.

---

## My Contribution - Module 2: Paper Verification

I was responsible for **Module 2: Paper Verification** - the reference validation and full-text retrieval component of the pipeline.

**Specifically, I built:**

- **CrossRef metadata validation** - verifying cited papers exist using title normalisation, author particle support, and fuzzy string similarity scoring (handling punctuation differences, capitalisation variants, and truncated titles that caused false mismatches in early testing)
- **3-tier full-text retrieval pipeline** - PMC → Unpaywall → PubMed abstract fallback, ensuring the system almost always retrieves something for downstream extraction
- **Reference verification functions** - checking DOIs, publication years, and author metadata against CrossRef records
- **Download functions** - retrieving accessible full-text where available for the data extraction stage

The title normalisation and flexible comparison logic I implemented significantly reduced false mismatch rates during paper verification, a key quality improvement identified during team testing.

---

## System Architecture - 4-Stage Pipeline

Input (PDF or YAML)
│
▼
Stage 1: Ingestion & Search Validation

PyMuPDF text extraction
LLM-driven structuring into StudentReviewInput schema
PubMed search reproduction via NCBI E-utilities API │
▼
Stage 2: Paper Verification ◄── MY MODULE
CrossRef metadata validation
Fuzzy title/author matching
3-tier full-text retrieval (PMC → Unpaywall → PubMed abstract) │
▼
Stage 3: Data Extraction
Dual-LLM extraction (Qwen-Plus + Gemini 2.5)
Independent extraction for cross-validation
Schema-based field alignment │
▼
Stage 4: Comparison & Reporting
6-state field classification (MATCH, PARTIAL_MATCH, DIFF,
MISSING_MODEL, MISSING_STUDENT, NEEDS_REVIEW)
Coverage and agreement metrics (tracked separately)
DOCX report with per-paper agreement tables and flag appendix

---

## Key Results (Demo)

- **3 papers** had sufficient coverage for meaningful agreement scores: 86%, 86%, 50%
- **6 papers** returned near-zero coverage (abstract-only fallback - insufficient for 22 detailed fields), correctly triggering PARTIAL rather than false FAIL
- **19 flags raised** across the 9-paper demo review
- **Verdict logic:** PARTIAL if coverage < 50% (human decides); FAIL if agreement < 60%; PASS if agreement ≥ 60%

---

## Team

| # | Name | Module | Role |
|---|------|--------|------|
| 1 | Osamende Ekhator | Module 1 | Document parsing, metadata extraction, NLP/LLM tasks |
| 2 | Noel James | Module 1 | Search pipeline validation, API programmatic searches |
| 3 | Wenyang Liu | Module 3/4 | Dual-LLM architecture, field-level scoring logic |
| 4 | **Vanshika Karnwal** | **Module 2** | **Paper retrieval, reference verification, download functions** |
| 5 | Varshini Umarani | QA | Quality assurance, report writing, cross-team coordination |
| 6 | Vikram Magesh | Module 3 | Extraction prompt design, output structuring |
| 7 | Adithyan Thirunavukkarasu | Module 4 | DOCX and JSON report generation |
| 8 | Ahmed Alenezi | Support | Verification support, retrieval checks, code reviews |

---

## Why This Project Matters

This project sits directly at the intersection of my clinical research background and AI engineering. Having conducted and published a systematic review myself (Egyptian Heart Journal, 2025), I understood exactly what manual verification involves, which made building an automated alternative both technically and clinically meaningful.

The paper verification module I built is the quality gate between search validation and data extraction. Without reliable reference verification, the entire downstream pipeline produces unreliable results. Getting fuzzy matching right for biomedical citations (where "CCTA" and "Coronary computed tomography angiography" are the same thing) required domain knowledge as much as engineering skill.

---

## How to Run

```bash
git clone https://github.com/vanshikakarnwal/systematic-review-integrity-checker.git
cd systematic-review-integrity-checker
pip install -r requirements.txt
python -m lit_inspector.main
```

---

## Limitations & Future Work

- Thresholds (50% coverage, 60% agreement) not yet empirically calibrated
- PDF parsing quality varies - scanned documents reduce reliability
- Full-text unavailable for many papers - abstract fallback limits extraction depth
- Future: OCR support, expanded database coverage beyond PubMed, benchmark dataset

---

*Forked from the original group repository. This fork documents my Module 2 contribution as part of my professional portfolio.*
