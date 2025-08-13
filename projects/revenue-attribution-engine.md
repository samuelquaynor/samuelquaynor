# Revenue Attribution Engine: Collection Platform Automation

## Background
Our collection platform (X-Collect) started for a single business unit to handle tax payments. As more units began using it, we needed a reliable way to see which revenue belonged to which business unit. That work was being done manually, was slow, and often wrong.

## The Problem
- Inconsistent identifiers
  - The platform stored payer names, not the legal business entity
  - Example: “John Smith (Google employee)” instead of “Google Inc.”
- Manual cross‑referencing
  - Interns downloaded PDFs from the tax authority site
  - Extracted the real business entity from each PDF
  - Matched that to an internal client ID in spreadsheets
- Data integrity issues
  - Name mismatches created incorrect mappings
  - Led to inter‑unit revenue disputes and delayed monthly/quarterly reporting

## The Solution
I designed an automated attribution pipeline inside X‑Collect that turns raw transactions into verified, report‑ready revenue ownership.

At a glance, the pipeline:
- Pulls new transactions from MSSQL on a schedule
- Fetches related PDFs from the authority site automatically
- Extracts the business entity name from each PDF (robust text extraction)
- Resolves that entity to an internal client ID using rules and similarity scoring
- Stores confirmed matches so future runs are instant and consistent
- Writes validated attributions into reporting tables that feed dashboards

Why these choices worked:
- Persistent match reference avoids re‑solving the same entities and compounds speed/accuracy over time
- Confidence thresholds automate high‑certainty cases and surface ambiguous ones for review when needed
- Batch processing and caching keep throughput high across thousands of records per day

## Results & Impact
- Secured and credited 32M+ GHS to the rightful business unit
- 99%+ attribution accuracy across thousands of daily transactions
- Reporting cycle shortened from days to minutes for this flow
- Eliminated inter‑unit friction caused by incorrect name mappings

## Technology
- Python — ingestion, PDF retrieval and parsing, entity resolution
- MSSQL — transactions plus a small, durable reference store of confirmed matches
- Power BI — live dashboards backed by validated attribution tables

## Where this happened
- Platform: X‑Collect (collection platform)
- Environment: Corporate engineering
- Role: Full‑stack ownership (backend, data engineering, system design)
- Timeline: ~3 months from problem to production

## What I learned
- Technical clarity creates business outcomes when tied to revenue ownership
- Small, persistent reference data creates compounding value over time
- In high‑stakes data flows, ownership accuracy matters as much as speed

---

*[Back to Portfolio](../README.md)*
