# Data Team Structure: From Chaos to a Lean, High‑Impact Model

## Background
Multiple teams needed analytics and reporting, but data work was scattered, ownership was unclear, and delivery cycles were slow. Fire‑drills were common, dashboards contradicted each other, and no one knew who was responsible for what.

## Overview
Built an intelligent operating model for the data function that correctly attributes ownership, accelerates delivery, and improves data quality across teams.

## Constraints
- Microsoft‑first: prioritize Power Platform, Power BI, MSSQL, Azure services
- Alternatives can be suggested, but adoption will require extra approvals
- Keep solutions simple, auditable, and easy to operate by the current team

## The Problem

### 1. MIS Request Workflow — Current State & Issues

- Workflow Breakdown:
  - When the business team raises an MIS request (e.g., "Give me monthly sales by region"), there is no formal intake system or queue. Anyone from the data team might pick it up informally.
  - The responding team member often looks through old Notepad files or previously used Power BI dashboards to see if a similar query exists. These are stored on local machines or in inconsistent folders—not a central, searchable repository.
  - If no query is found, the developer writes a fresh SQL query from scratch.
  - They then remote into a central server (via RDP or similar) to access the DB and run the query.
  - Data is copied to Excel, formatted manually, and either emailed or embedded in PowerPoint for reporting.
  - The process may repeat in future if the same question is asked again, by another person.

- Problems:
  - ❌ No centralized request tracking system
  - ❌ Duplicate work due to lack of reusable SQL asset library
  - ❌ Query logic is not version-controlled or documented
  - ❌ Server access is manual and dependent on individual availability
  - ❌ Data transformations are ad hoc and done in Excel—not traceable
  - ❌ No historical tracking of what logic was applied or what version of data was delivered

### 2. Power BI Workflow — Friction Points

- Current Flow:
  - Developers typically start by copying SQL queries from past dashboards.
  - They update connection credentials manually every time (hostname, port, credentials).
  - Transformations happen entirely in Power BI via Power Query, with no documentation outside the PBIX file.
  - Reports are published directly to the Power BI Service — but there's only one shared workspace, with no environment separation (dev, staging, prod).
  - Changes go live as soon as published — without peer review or validation.
  - Metric logic is not standardized — different reports often compute the same KPI (e.g., “conversion rat”) in slightly different ways.

- Problems:
  - ❌ No templates for report creation — everything is copy-paste based
  - ❌ Connection strings and credentials are manually retyped every time
  - ❌ No version control or collaborative editing (e.g., Git integration)
  - ❌ Power BI environment is flat — no dev vs. prod separation
  - ❌ Logic is often duplicated with inconsistencies between reports
  - ❌ No documentation or definitions for calculated measures and KPIs

### 3. Automation / RPA Workflow — Structural Gaps

- Current Flow:
  - When a request for automation comes in (e.g., scraping data, formatting Excel, uploading files), a developer typically jumps straight into coding using VS Code or another editor.
  - There’s no architectural guidance or reusable component framework.
  - Git is not used; files are developed and stored locally.
  - Once developed, the code is copied manually to a server for execution.
  - Scheduling is done via Windows Task Scheduler or a Jenkins instance, but setup is often manual and undocumented.
  - There is no formal testing, no exception logging, and no rollback plan.

- Problems:
  - ❌ Zero version control — no Git repo, no branching or code reviews
  - ❌ No documentation for architecture, parameters, or output
  - ❌ Testing is skipped — bugs are caught post-deployment
  - ❌ Deployment is done manually via copy-paste to server
  - ❌ No way to recover gracefully from errors
  - ❌ Jenkins jobs are manually configured, undocumented, and fragile

### 4. Power Apps & Power Automate — Governance Gaps

- Current Flow:
  - Everyone uses the default “Environment” provided by Power Platform. There is no segregation between dev/test/prod.
  - Solutions are developed in personal workspaces, invisible to others unless explicitly shared.
  - Users create flows and apps on an ad hoc basis, often duplicating similar logic.
  - Power Automate flows are published with minimal testing, and any error impacts live data.

- Problems:
  - ❌ No environment separation — everything is developed and deployed in a single space
  - ❌ No shared governance — anyone can build anything, leading to duplication and chaos
  - ❌ Flows and apps live in individual accounts — high risk of abandonment when someone leaves
  - ❌ No central registry or dashboard to track what automations exist and what they do

### 5. Database Management — Ownership & Planning Issues

- Current Flow:
  - The data team does not own its own database. Instead, they use a mix of tables owned by other engineering teams (e.g., SalesOps, Product).
  - If a new table is required, someone just creates it in whatever schema is available — without naming conventions, documentation, or approvals.
  - Over time, similar tables with slightly different logic accumulate, and no one knows which is the authoritative version.
  - There is no separate dev or staging DB environment to test queries before running them on production.

- Problems:
  - ❌ No database ownership — team is dependent on others for changes
  - ❌ Table sprawl — duplicate or overlapping tables cause confusion
  - ❌ Lack of standards — no consistent naming, documentation, or governance
  - ❌ No separation of environments — risky to test on live production data

### 6. Data Sharing & Knowledge Retention — Invisible Risks

- Typical Scenario:
  - A requestor asks for a dataset or dashboard. The data team prepares it (possibly via Excel or PowerPoint) and sends it out via email or Teams.
  - Over time, different people use this output — sometimes modifying filters or assumptions — and repurpose it without checking back with the original creator.
  - Eventually, someone reports discrepancies ("Why is my conversion rate different this month vs. last?") because logic or filters were changed without understanding the context.

- Problems:
  - ❌ Data is shared in transient formats (Excel, PowerPoint) without source traceability
  - ❌ Recipients modify data without understanding original logic
  - ❌ No single source of truth — reused data becomes unreliable
  - ❌ Team members leave, and undocumented logic goes with them

### 7. Cross‑Cutting Issues (Across All Workflows)
- ❌ No centralized repository for queries, dashboards, or automations
- ❌ No version control practices across tools
- ❌ No testing or peer review mechanisms
- ❌ No dev/prod separation in any platform (Power BI, Power Apps, DB)
- ❌ Reuse of logic is tribal and undocumented — highly person‑dependent
- ❌ No scalable governance or visibility — everything is in silos

## Solutions (MIS workflow only)

### MIS Request Workflow

```
Business Request
       ↓
Power Apps Form
       ↓
Power Automate
       ↓
Azure Boards Ticket
       ↓
Weekly Triage
       ↓
Assign Owner & Priority
       ↓
VS Code + Git
       ↓
Write SQL + Tests
       ↓
Pull Request
       ↓
Peer Review
       ↓
Tests Pass? ──No──→ Back to Development
       ↓ Yes
Recurring?
   ↙        ↘
  Yes        No
   ↓         ↓
SQL Server   PowerShell
Agent        Runner
   ↓         ↓
Power BI     CSV to
Dataset      SharePoint
   ↓         ↓
Deployment   Share Link
Pipeline
   ↓
Certify Dataset
   ↓
Close Ticket
   ↓
Link to SQL/Dataset/Output
```

### Tools we’ll use (and why)
- **VS Code + mssql**: write and run SQL in one place; replace Notepad/SSMS
- **Azure DevOps Git**: every query is versioned and reviewed
- **Power Apps + Power Automate + Azure Boards**: simple intake form → tracked ticket
- **SQL Server Agent**: reliable schedule for recurring reports
- **PowerShell**: run one‑off queries and export to CSV cleanly
- **Power BI Deployment Pipelines**: promote datasets Dev → Test → Prod without copy‑paste
- **SharePoint**: versioned file delivery when CSVs are required
 - **Oracle client/SQLcl on the agent + Power BI gateway Oracle source**: run Oracle SQL and/or connect datasets

### Roles for each request (clear ownership)
- **Requester (Business)**: states question, filters, deadline, acceptance sample
- **Analyst/Engineer (Owner)**: builds SQL, tests, and delivery artifact
- **Reviewer (Peer)**: reviews logic and checks; approves PR
- **Product Owner (Data PO)**: prioritizes in triage; signs off on acceptance criteria

### Step‑by‑step flow (with details)
1) **Intake**
- Power Apps form: business question, filters (dates, regions), due date, sample expected numbers
- Power Automate creates an Azure Boards ticket (type: User Story) with attachments

2) **Plan**
- Weekly triage: set priority, assign owner, define acceptance criteria (what numbers should match)
- Break down into tasks (staging, report SQL, tests, Power BI/CSV delivery)

3) **Build in VS Code (Git)**
- Create or update SQL files under `mis/`
- Add a short header to explain inputs/rules; avoid `SELECT *`; use clear CTEs
- Parameterize with SQLCMD variables when needed (e.g., date range)

4) **Review (Pull Request)**
- Reviewer checks: correctness, readability, performance (indexes/joins), and tests exist
- Small changes only; if large, split into smaller PRs

5) **Test (simple and automated)**
- Freshness, uniqueness, row‑count delta, basic KPI sanity checks as SQL files in `tests/`
- Run locally in VS Code, and automatically via:
  - Self‑hosted Azure DevOps agent (preferred), or
  - Power Automate via on‑prem gateway, or
  - SQL Server Agent job calling a test stored procedure

6) **Run**
- Recurring: materialize a stable `rpt_` view/table with SQL Server Agent (nightly/hourly)
- One‑off: PowerShell script executes the versioned SQL and exports CSV to SharePoint

7) **Deliver**
- Power BI dataset points to `rpt_` objects; certify when reused by others
- For CSV, send SharePoint link (versioned), not email attachments

8) **Close the loop**
- Compare against acceptance sample; if matched, close ticket
- Link ticket to SQL file, dataset/report, SharePoint output, and tests

### Oracle support (two patterns)
- **Option A — Dual‑source (no ETL):**
  - Build `rpt_` views in both Oracle and SQL Server.
  - Power BI connects to both via the on‑premises data gateway (define one data source per DB).
  - Use dataset parameters and Deployment Pipelines rules to switch Dev/Test/Prod.
- **Option B — Stage Oracle into SQL Server (recommended for MIS):**
  - Nightly job copies the small Oracle slices you need into SQL Server `stg_oracle_*` tables.
  - Build all MIS `rpt_` outputs in SQL Server so Power BI uses a single source.
  - Implement copy via Linked Server or PowerShell + Oracle Managed Data Access.

### Standards & conventions
- Folder structure (keep it small and consistent)
  - `mis/stg/` – quick cleanups
  - `mis/rpt/` – report‑ready outputs with a stable schema
  - `mis/tests/` – test definitions (freshness, uniqueness, row deltas, KPI sanity)
  - `mis/tests/oracle/` – Oracle‑specific tests (if dual‑source)
  - `mis/docs/` – one short page per report (purpose, inputs, rules, owner)
  - `mis/scripts/` and `mis/scripts/oracle/` – helper run tasks (described, not coded inline)
- Each SQL file starts with a brief header answering: title, purpose (ticket ID), inputs/filters, key business rules, owner
- Naming: `stg_` for staging, `dim_`/`fct_` when modeling is needed, `rpt_` for final, stable outputs
- Style: avoid wildcard selects; prefer clear CTEs and commented business rules

### Quality checks to run (no code in repo)
- Freshness: latest data date is within the agreed SLA window
- Uniqueness: key fields (e.g., region keys) have no duplicates
- Row‑count delta: day‑over‑day or week‑over‑week volume within expected range
- KPI sanity: basic validations (e.g., no negative amounts after exclusions)
- Document expected thresholds per report in `mis/docs/`

### Automation options (pick one)
- **Self‑hosted Azure DevOps agent (recommended)**: runs tests and scripts from Git inside your network
- **Power Automate via on‑premises gateway**: schedules SQL actions and alerts via Teams/email
- **SQL Server Agent**: schedules stored procedures that run tests and load `rpt_` tables/views

### How we run one‑off requests (procedure)
- Use a predefined task in the self‑hosted agent to execute a versioned SQL file and save output to a versioned SharePoint location
- Name outputs consistently (folder/report/date); link back to the Azure Boards ticket
- For Oracle, the agent task uses the installed Oracle client; for SQL Server, it uses native SQL tools

### Connection variables & secrets (how we store them)
- Non‑secret environment parameters (server names, DB names, gateway data‑source names) live in small env files in Git (Dev/Test/Prod)
- Secrets (usernames, passwords, connection strings) live in Azure DevOps variable groups; injected into agent jobs at runtime
- Developers keep local VS Code connection profiles; no passwords ever committed
- Power BI gateway holds data‑source credentials for SQL Server and Oracle; Deployment Pipelines map parameters per environment

### Observability (simple and useful)
- Keep a small run log table to trace what ran and when (run ID, job name, start/end time, status, rows output, notes)
- Show key counters in a Power BI page (runs per day, failures, row counts)

### Security & secrets
- Use service accounts (least privilege) for Agent/automation
- Keep secrets out of Git: use Azure DevOps variable groups or gateway connections
- Grant read on `rpt_` objects; restrict write access to the data team

### PR checklist (keep it lightweight)
- [ ] SQL readable and commented where needed
- [ ] Tests added/updated and pass locally
- [ ] Docs/header updated (purpose, inputs, rules, owner)
- [ ] Performance sane (no obvious full scans on big tables)

### Board workflow (simple states)
- New → Triaged → In Progress → In Review → Scheduled/Run → Delivered → Done

### Environments & promotion
- Dev/Test/Prod databases; VS Code connection profiles per env
- Power BI Deployment Pipelines for dataset/report promotions
- Promote only when tests pass and acceptance criteria are met

## Agile/Scrum Operating Model
- Roles
  - Data Product Owner (business‑aligned), Scrum Master (data team lead), Delivery Team (Analyst + Data Engineer)
- Sprint cadence
  - 2‑week sprints; plan at start, release on cadence through Deployment Pipelines
- Ceremonies
  - Backlog refinement weekly (value scoring, Definition of Ready, acceptance criteria)
  - Daily stand‑up (15 min): blockers, priorities, handoffs
  - Sprint review/demo with stakeholders at end of sprint
  - Retrospective with 1–3 concrete improvements tracked in Azure Boards
- Artifacts & tooling
  - Azure Boards: Epics, Features, User Stories, Tasks, Bugs; Sprint backlog per iteration
  - DoR/DoD: Ready = source known, owners identified, success metric defined; Done = tests pass, docs updated, promoted to next environment
  - Estimation: story points or t‑shirt sizes; capacity‑based planning
- Metrics
  - Burndown, cumulative flow, velocity, lead time; visible to stakeholders

## The Transformation Impact: Team & Individual Growth

### What This Changes for Us

**Before:** We were always putting out fires. Someone would ask for data, we'd scramble to get it done, and then do it all over again next time.

**After:** We build things that last. We create systems that work reliably, and everyone can understand and use them.

### How This Helps Everyone

**For the Team**
- **No More Surprises**: We know what we're working on and when it's due
- **We Can Handle More**: One person can do what used to take three people
- **Fewer Mistakes**: Our tools catch problems before they reach the business
- **Knowledge Stays**: When someone leaves, their work doesn't disappear
- **We Work Together**: Code reviews, shared templates, and collaborative development
- **Knowledge Sharing**: Everything is documented and everyone can learn from each other

**For Each Person**
- **Learn New Skills**: Git, testing, automation - things that make us better engineers
- **Clear Path Forward**: We can see how our work matters and where we're going
- **Help Each Other**: Anyone can pick up any project; no one is stuck
- **Time for Big Ideas**: Less firefighting means more time for interesting projects
- **Learn from Peers**: Code reviews and shared knowledge make everyone better
- **Share What You Know**: Your work helps others learn and grow

**For the Business**
- **We Deliver on Time**: When we say something will be ready, it is
- **Our Data is Reliable**: Clear ownership and tracking of what we build
- **We Save Money**: Reusing work instead of starting from scratch
- **Better Collaboration**: Teams work together instead of in silos
- **Knowledge Transfer**: When people leave, their knowledge stays with the team

### Career Growth

**Technical Path**: Data Analyst → Analytics Engineer → Data Engineer
**Leadership Path**: Data Analyst → Senior Analyst → Team Lead

### The Bottom Line

We go from being firefighters to being engineers. We build systems instead of just putting out fires. Everyone grows, the team gets stronger, and the business gets better data faster.

## What the Business Will See and Expect

### Immediate Changes (First 30 Days)
- **Faster Response Times**: MIS requests that used to take days now get done in hours
- **No More "I Don't Know"**: Clear status updates on every request through Azure Boards
- **Consistent Quality**: Reports that used to have errors now work reliably
- **Better Communication**: Weekly updates on what's being worked on and what's coming next

### Short-Term Improvements (1-3 Months)
- **Predictable Delivery**: When you ask for data, you get a clear timeline and it actually happens on time
- **Self-Service Options**: Simple reports available through Power BI without needing to ask the data team
- **No More Duplicate Work**: If someone asks for the same data twice, it's already built and ready
- **Better Insights**: Data is more accurate and consistent across different reports

### Long-Term Benefits (3-6 Months)
- **Strategic Projects**: The data team has time to work on big-picture analytics instead of just putting out fires
- **Data-Driven Decisions**: Reliable data means better business decisions
- **Cost Savings**: Less time spent on repetitive data requests means lower operational costs
- **Competitive Advantage**: Faster insights give the business an edge over competitors

### What Business Users Can Expect
- **Clear Request Process**: Simple form to submit data requests with clear expectations
- **Regular Updates**: Know exactly where your request stands and when it will be ready
- **Reliable Data**: Trust that the numbers you're seeing are accurate and up-to-date
- **Faster Answers**: Get the data you need when you need it, not weeks later
- **Better Tools**: Access to dashboards and reports that actually work and are easy to use

### What Leadership Will Notice
- **Reduced Data Fires**: No more emergency calls about "wrong numbers" or "missing data"
- **Better Planning**: Data team can provide insights for strategic planning instead of just reacting
- **Improved ROI**: Data investments pay off faster with more reliable and accessible information
- **Team Growth**: Data team members are happier, more productive, and growing professionally

---

*[Back to Portfolio](../README.md)*
