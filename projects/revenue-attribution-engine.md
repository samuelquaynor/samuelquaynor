# Revenue Attribution Engine: Collection Platform Automation

## 🎯 Overview
Built an intelligent automation system that solved a critical revenue attribution problem within our collection platform. The solution transformed manual, error-prone tax data processing into an automated system that correctly revenue to the right business units.

## 🏢 Environment Context
- **Environment Type:** Corporate Engineering
- **Team Size:** Solo project with cross-functional stakeholders
- **Timeline:** 3 months from problem identification to production deployment
- **My Role:** Full-Stack Engineer (Backend, Data Engineering, System Architecture)

## The Problem
- The platform stored payer names, not the legal business entity.
  - Example: “John Smith (Google employee)” instead of “Google Inc.”
- Interns manually:
  - Downloaded PDFs from the tax authority site
  - Extracted the real business entity
  - Matched it to an internal client ID in spreadsheets
- This led to name mismatches, revenue disputes between units, and delayed monthly/quarterly reporting.

## Approach (Engineer’s view)
Design a simple, durable pipeline that turns raw transactions into verified attributions:
- Pull new transactions from MSSQL on a schedule
- Fetch the related PDFs from the authority site
- Parse the PDFs to isolate the business entity name
- Resolve that name to an internal client ID
- Store confirmed matches so future runs are instant
- Push validated attributions to reporting

## Architecture at a Glance
- Python workers for ingestion, PDF retrieval, parsing, and attribution
- MSSQL for transactions plus a small reference store of confirmed matches
- Power BI dashboards fed by validated attribution tables

## Key Engineering Decisions
- Persistent match reference: Save confirmed pairs to avoid re‑solving the same names; lookups drop from minutes to milliseconds over time.
- Confidence thresholds with human-in-the-loop: Automate the obvious; surface the ambiguous.
- Batch processing and caching: Efficient daily throughput across thousands of records.

## Results
- Recovered and secured ~32M GHS to the correct business unit
- Accuracy above 99% across thousands of daily transactions
- Reporting cycle reduced from days to minutes for this flow
- Eliminated inter‑unit disputes caused by name mismatches

## What I Owned
- Problem framing with finance/ops
- System design and implementation (Python, MSSQL)
- PDF parsing and name resolution
- Data models feeding Power BI
- Rollout and iteration based on stakeholder feedback

## Lessons Learned
- Technical clarity creates business outcomes when tied to revenue ownership
- Small, persistent reference data can compound accuracy and speed
- In high‑stakes data flows, ownership accuracy matters as much as processing speed

---

[Back to Portfolio](../README.md)
