# Executive Insights Module — How It Works

> **Document type:** Product walkthrough — end-to-end usage across org sizes and industries
> **Audience:** Product, Sales, Customer Success, Implementation
> **Companion to:** `product-analysis.md` (technical spec)
> **Last updated:** 2026-10-28

---

## Table of Contents

1. [The Core Loop](#1-the-core-loop)
2. [How It Works for Each Org Size](#2-how-it-works-for-each-org-size)
   - [500–1,000 Employees](#slab-1--5001000-employees)
   - [1,000–2,500 Employees](#slab-2--10002500-employees)
   - [2,500–5,000 Employees](#slab-3--25005000-employees)
   - [5,000–10,000 Employees](#slab-4--500010000-employees)
   - [10,000+ Employees](#slab-5--10000-employees)
3. [How It Works Across Industries](#3-how-it-works-across-industries)
   - [Technology & Software](#technology--software)
   - [Financial Services & Banking](#financial-services--banking)
   - [Retail & Consumer Goods](#retail--consumer-goods)
   - [Healthcare & Life Sciences](#healthcare--life-sciences)
   - [Manufacturing & Supply Chain](#manufacturing--supply-chain)
   - [Professional Services](#professional-services)
4. [The Setup Journey (All Configurations)](#4-the-setup-journey-all-configurations)
5. [Day-in-the-Life Scenarios](#5-day-in-the-life-scenarios)

---

## 1. The Core Loop

Regardless of org size or industry, the Executive Insights module runs the same loop for every user session:

```
Orient → Explore → Diagnose → Decide
```

| Phase | What the user does | What the product shows |
|---|---|---|
| **Orient** | Opens the module; takes in the org-wide picture | Function cards with activity rings, headline KPI tiles (functions, job families, skills, role instances) |
| **Explore** | Clicks into a function; spins through the sunburst to understand shape and depth | Zoomable sunburst: job families as wedges, capabilities as sub-wedges; side list for text navigation |
| **Diagnose** | Drills into a capability; reviews skills, proficiency requirements, and sourcing flags | Skill clusters, proficiency ladder definitions (Basic / Intermediate / Advanced), BBBB badges, role × skill matrix |
| **Decide** | Walks away with a signal for L&D investment, hiring brief, or automation roadmap | Build = design a training programme; Buy = open a JD; Borrow = engage a partner; Bot = add to automation backlog |

This loop is the same whether the user is a CHRO at a 600-person fintech or a People Analytics Director at a 50,000-person manufacturer. What changes is the breadth of the map and the vocabulary on it.

---

## 2. How It Works for Each Org Size

---

### Slab 1 — 500–1,000 Employees

**Who uses it:** Founders, COOs, Heads of People. Rarely a dedicated HRBP.

**The problem it solves:** At this size, capability data lives in the founder's head, a few Excel sheets, and one or two JDs. There is no shared language for "what skills does our Finance team actually need?" When you're hiring or planning a reorg, you're guessing.

#### How it works

**Setup (week 1–2):**
The implementation team runs a half-day workshop with the Head of People and 2–3 function leads. Using the pre-built starter taxonomy for the relevant industry, they identify which functions exist (typically 6–10), confirm the job family names, and flag any roles that are already in the hiring pipeline. The taxonomy is loaded into a JSON configuration file — no code changes required.

**First use:**
The COO opens the module and sees a clean landing page with one card per function. Each card has a small activity ring showing how many job families sit inside. The stat strip shows the whole picture: 8 functions, 32 job families, 210 capabilities, 640 skills mapped.

They click into **Engineering**. The sunburst shows three job families: Frontend, Backend, Platform. They click **Platform Engineering** and see the capability open up. The side list shows the skill clusters: Cloud Infrastructure, CI/CD & DevOps, Security Hardening, AI & Automation.

They drill into **Cloud Infrastructure**. They see 12 skills, each with a Build/Buy/Borrow/Bot flag and a Core/Sunrise/Sunset label. Three skills are flagged Sunrise: Platform Engineering, FinOps, and AI Agents. Two are flagged Bot: Manual Environment Provisioning, Manual Cost Reporting. The role × skill matrix shows that their Senior Engineer role requires Advanced in Cloud Architecture but only Basic in FinOps — a gap signal before they even run a formal skills assessment.

**The decision it drives:**
The COO takes a screenshot of the Platform Engineering matrix and shares it in the next leadership review. The result: the Head of Engineering agrees to add FinOps to the Q3 L&D plan rather than hiring a dedicated FinOps specialist (Build, not Buy).

**What this slab does NOT include:**
- Sub-Job Family layer (too granular for a 600-person org)
- Multi-workbook federation
- Role-based access control (everyone sees everything)
- API integration (data is managed via CSV import or JSON config)

---

### Slab 2 — 1,000–2,500 Employees

**Who uses it:** CPO / CHRO, HRBPs aligned to functions, L&D lead, Talent Acquisition lead.

**The problem it solves:** The org has matured past the "everyone knows everyone" stage. Functions are real. Job families are real. But the skills taxonomy is fragmented — L&D has a competency framework, TA has a set of JD templates, and HRBPs have a spreadsheet. None of them talk to each other. A new HRBP joining the Commercial function has no way to quickly understand what "Category Management" actually requires at each grade.

#### How it works

**Setup (2–3 weeks):**
The implementation team runs a function-by-function discovery with each HRBP (one session per function, 90 minutes each). The standard industry taxonomy is used as a starting point; HRBPs confirm, rename, or add clusters and skills. At this slab, the Sub-Job Family layer is introduced — each job family gets 2–4 sub-job families that match how teams actually work. The resulting taxonomy is imported via the admin Excel template.

**First use — HRBP for Finance:**
The Finance HRBP opens the module and navigates to the Finance function. The sunburst shows 6 job families: Financial Planning & Analysis, Financial Reporting & Control, Tax, Treasury, Finance Business Partnering, Finance Systems & Technology. She clicks **Financial Planning & Analysis**, which zooms in and reveals three sub-job families: Commercial FP&A, Group FP&A, Business Finance.

She clicks **Commercial FP&A**. The detail view shows 4 skill clusters: Financial Modelling & Analysis, Business Partnering & Influence, Commercial Strategy, and AI & Automation. She expands the **Financial Modelling & Analysis** cluster. Each skill card shows:
- Whether it is Core or Sunrise (three skills are Sunrise: AI-assisted Forecasting, Scenario Modelling with ML, Automated Reporting)
- Whether to Build, Buy, Borrow, or Bot it
- The proficiency ladder: what Basic looks like for a Finance Analyst vs. what Advanced looks like for a Finance Director
- The "Show role proficiency" toggle — she clicks it on **Financial Modelling** to see that her WL1-Senior Associate grade needs Intermediate but WL2-Manager needs Advanced

**The decision it drives:**
The HRBP is building a development plan for three Finance Analysts who are ready for promotion. She can now articulate exactly what Advanced in Financial Modelling looks like (from the `advanced_def` text) and use it as the benchmark in their PDPs. The L&D team uses the Build flags to prioritise which skills to build a programme for vs. which to source externally via a learning vendor (Buy).

**What this slab adds over Slab 1:**
- Sub-Job Family layer enabled
- Per-function stat strip on the sunburst view
- Excluded-workbook banner (if a function's data is incomplete)
- Excel import for taxonomy authoring
- HRBP-level personas with function awareness

---

### Slab 3 — 2,500–5,000 Employees

**Who uses it:** CHRO, Workforce Planning Lead, Capability COE, Function Heads, HRBPs.

**The problem it solves:** The org is big enough that no single person holds the whole capability picture. The CHRO needs to present a board-level view of "what kind of organisation are we becoming?" — Build-heavy or Buy-heavy? AI-ready or AI-lagging? But the data is siloed across HR systems, L&D platforms, and function-owned spreadsheets. A single view that every function head trusts and recognises doesn't exist.

#### How it works

**Setup (4–6 weeks):**
The implementation team runs a capability mapping sprint per function, facilitated by a Capability COE lead if one exists. Each function typically produces a workbook (Excel template) defining its sub-job families, roles, clusters, and skills with proficiency definitions and BBBB flags. Functions without proficiency data are flagged in the excluded-workbook banner until their data is complete. The combined taxonomy is validated and loaded. At this slab, the org starts to see **multiple business unit workbooks** that are federated into a single view.

**Typical workflow — Workforce Planning review (quarterly):**

1. The Workforce Planning Lead opens the module ahead of the quarterly review. The landing page stat strip immediately shows: 18 functions, 104 job families, 312 capabilities, 1,840 skills.

2. She navigates to **Supply Chain** — one of the three functions flagged for headcount growth in the business plan. The sunburst shows 7 job families. She zooms into **Procurement** and then into **Strategic Sourcing**.

3. In the detail view, she notices that **Supplier Sustainability Assessment** is flagged Sunrise + Buy (no internal talent) + Bot (partially automatable). This is a skill the business needs but doesn't have. She notes it as a "critical gap" for the headcount review.

4. She uses the global search to find all instances of **"Negotiation"** across the org. The search returns 14 results spanning 6 functions — same skill, different contexts. She navigates to the Supply Chain and Commercial instances and compares the proficiency definitions. They are different — Supply Chain focuses on contract terms, Commercial focuses on pricing and terms of trade.

5. At the board review, the CHRO presents a view from the module: "We have 18 functions. 23% of our skills are flagged Sunrise — our AI readiness is concentrated in 4 functions. 6% of skills are flagged Sunset — these are our active capability deprecation signals." This is a 5-minute data pull from the module, not a 3-week analytical project.

**What this slab adds over Slab 2:**
- Multiple business unit workbooks federated into one view
- Excluded-workbook banner with explicit function naming
- CrossFunction / rotation role types supported
- Full WL-grade taxonomy
- Quarterly snapshot versioning (planned)

---

### Slab 4 — 5,000–10,000 Employees

**Who uses it:** Chief People Officer, Head of Talent, People Analytics Director, L&D Director, Talent Acquisition Director.

**The problem it solves:** The org is complex enough that different stakeholders need different access. The Head of Commercial should be able to see everything in Commercial, but nothing in Finance. The People Analytics team needs to query across functions for strategic projects but not expose raw data to individual managers. At this size, the capability framework also needs to align to compensation bands and career frameworks that exist in the HRIS.

#### How it works

**Setup (6–10 weeks):**
The implementation runs in parallel workstreams: one for data (HRIS integration, taxonomy validation), one for access (RBAC configuration), one for alignment (matching playbook grade taxonomy to HRIS grade codes). A Global Job Architecture (GJA) mapping table is created: every role in the playbook is mapped to its HRIS job code and grade band. The HRIS provides the live role population — the playbook enriches each role with skills, proficiency, and sourcing data.

**Two distinct usage patterns exist at this slab:**

**Pattern A — Strategic (CPO / People Analytics Director):**
The CPO opens the module for a Board presentation on workforce readiness. She navigates to the landing page and immediately reads the org-wide Sunrise skill concentration: 34% of skills flagged as Sunrise are in Digital & Technology and Data & AI functions. The remaining 66% are spread unevenly — Legal & Risk has 8% Sunrise skills, Finance has 12%. She pulls this as a signal for where AI upskilling investment should prioritise (Finance and Legal — lower AI readiness relative to their headcount).

**Pattern B — Operational (Function Head — RBAC scoped):**
The Head of Engineering logs in. She sees only the Engineering function. The landing page shows her 6 job families, 42 capabilities, 380 skills. She navigates to **Platform Engineering** → **Site Reliability** and reviews the skill × role matrix for her Principal Engineer population. The matrix shows that 70% of Principal Engineers are mapped at Intermediate in Incident Management — she expected Advanced. She flags this for the L&D team.

**The integration story:**
At Slab 4, the module connects to the HRIS. Role population data flows in automatically: when a new role is created in Workday, it appears in the playbook within 24 hours. When an employee is promoted, their grade updates in the matrix. The playbook stays in sync without manual CSV exports.

**What this slab adds over Slab 3:**
- RBAC: function-scoped access per function head
- HRIS integration (role + grade sync)
- Extended color palette (12+ job families per function)
- Horizontally scrollable matrix for large role populations
- Geography / location filter on stat strip (for multi-site orgs)

---

### Slab 5 — 10,000+ Employees

**Who uses it:** Chief People Officer, Group Heads of Talent / Capability, People Analytics, Strategic Workforce Planning, Regional HR Directors.

**The problem it solves:** At this scale, the capability playbook is not a tool — it is an infrastructure asset. The skills taxonomy drives hiring decisions, compensation benchmarking, internal mobility, L&D vendor selection, and workforce planning. It needs to be versioned, auditable, and integrated with multiple upstream and downstream systems. A single taxonomy owner doesn't exist — different functions and regions govern their own sub-taxonomies, but the org needs a shared language at the top.

#### How it works

**Setup (12–20 weeks):**
The implementation runs as a programme, not a project. It typically involves a Global Job Architecture alignment exercise (mapping the playbook to an existing Hay/Mercer/WTW framework), a skills ontology governance model (who owns which function's taxonomy, what the change management process is), and a phased rollout by region or business unit. A pilot function is stood up in weeks 1–6; the full taxonomy is built out iteratively over the following 12 weeks.

**The strategic workflow — Annual Workforce Planning cycle:**

1. **September: Taxonomy refresh.** The Capability COE runs the annual skills review. Sunrise skills that have matured to standard practice are promoted to Core. Skills on the Sunset list are formally deprecated — removed from new JDs, L&D programmes, and hiring briefs. Three new Sunrise skills are added following the AI governance working group: Agentic Workflow Design, AI Risk Auditing, and Multi-model Orchestration.

2. **October: Snapshot published.** The refreshed taxonomy snapshot is published. All users see a "Updated October 2026" indicator. The prior snapshot is archived and available for year-on-year comparison.

3. **November: Strategic Workforce Planning input.** The People Analytics team exports the role × skill matrix data via API into their workforce planning model (Power BI / Anaplan). They overlay headcount data from the HRIS to calculate the gap between required proficiency levels and assessed proficiency levels for each function. The output is a "skills gap heat map" — 6 functions are in the red zone (>30% of critical skills at Basic when Advanced is required).

4. **December: Board presentation.** The CPO presents: "We have 32 functions, 8,400 skills in taxonomy. 28% are Sunrise. Our top 3 gaps requiring Buy decisions are [X, Y, Z]. Our top 5 Bot candidates are [A, B, C, D, E]." This narrative comes directly from the module. No manual data assembly.

**The internal mobility story:**
An employee in the Finance function is exploring a move to the Data & AI function. She opens the module, navigates to Data & AI → Business Intelligence → Analytics Engineering. She sees the skill requirements for the Analyst role she is targeting. She compares it to her current role's skill requirements in Finance → Financial Planning → Commercial FP&A. There is significant overlap in Data Literacy and SQL. The gap is in Python and ML Foundations — she knows exactly what she needs to develop before applying.

**What this slab adds over Slab 4:**
- API-first data layer; no embedded JSON
- Skill versioning with quarterly snapshots
- Multi-tenant isolation per business unit or region
- Bidirectional integration with talent marketplace (skills sync)
- Immutable audit log for taxonomy changes
- Employee-facing view (read-only self-service, scoped to own function + target roles)

---

## 3. How It Works Across Industries

The navigation model, skill lifecycle system, and BBBB sourcing framework are identical in every industry. What changes is the **function taxonomy**, the **vocabulary of skills**, and the **typical decision that leadership makes after using the module**.

---

### Technology & Software

**Typical entry point:** Head of Engineering or CPO at a Series C+ company (Slab 1–2) or a public software company's CHRO (Slab 3–4).

**How they use it:**
The module is used to solve the classic tech scaling problem: every function is growing, the skills needed 18 months ago are not the same as the skills needed today, and the engineering career ladder is always one reorg behind the actual specialisation happening on the ground.

The Engineering function sunburst shows job families that mirror the org's technical stack: Frontend, Backend, Data & ML, Platform, Security, Mobile. Clicking into **Data & ML** reveals sub-job families that reflect today's reality: ML Engineering, Data Engineering, Analytics Engineering, AI Product. The Sunrise cluster is rich: LLM Fine-tuning, RAG Pipelines, Agentic Systems, Evaluation & Red-teaming.

**The decision it typically drives:**
The CTO and CPO review the Bot flags together. Skills like Automated Testing, Infrastructure Provisioning, and Code Documentation are all flagged Bot — these are candidates for AI-assisted tooling investment, which directly affects headcount planning for junior engineering roles.

**Industry-specific nuance:**
Tech companies often have **dual career ladders** (IC track and management track). The role × skill matrix handles this — IC skills like System Design and Architecture are distinct from management skills like Engineering Strategy and Organisational Effectiveness, and the proficiency ladder reflects this separation.

---

### Financial Services & Banking

**Typical entry point:** CHRO or Head of Talent at a bank, insurance company, or asset manager. Most commonly enters at Slab 3–4 driven by a regulatory skills gap (e.g. Basel IV, DORA, operational resilience).

**How they use it:**
Regulatory change is the forcing function. The Compliance function is the most active part of the module — HRBPs and L&D leads use it to map which roles need to be upskilled for new regulatory requirements, and whether those skills need to be Built (training) or Bought (hiring certified specialists).

The Risk function sunburst shows job families: Credit Risk, Market Risk, Operational Risk, Model Risk, Regulatory Affairs. Drilling into **Regulatory Affairs** → **Regulatory Reporting** shows a skills cluster: COREP/FINREP Reporting, Basel IV Capital Calculation, IFRS 9 Provisioning, and — in the Sunrise cluster — RegTech Automation, AI Model Governance, ESG Regulatory Reporting.

**The decision it typically drives:**
The Head of Risk presents the module view to the CRO ahead of a regulatory examination. The argument: "We have 12 skills in Regulatory Reporting that are flagged Buy, and only 4 of our 23 analysts are at Advanced. We need to either train 19 people to Advanced by Q4 or hire 8 specialists. Here is the cost/risk comparison." The module makes this conversation concrete and fast.

**Industry-specific nuance:**
Financial services has **mandatory certification skills** (CFA, FRM, ACCA, solicitor qualification) that sit alongside behavioural skills. These are modelled as distinct Technical skills with the `category: "Core"` flag and `buy: true` (because you cannot build a CFA — you hire it or wait 3 years for someone to earn it).

---

### Retail & Consumer Goods

**Typical entry point:** CPO or Head of Capability at a multi-category retailer or a branded consumer goods company. Enters at Slab 2–4.

**How they use it:**
Retail has a unique challenge: the commercial function (Buying & Merchandising) is the engine of the business, but the skills it needs are changing faster than most organisations can track. Category Management is being disrupted by AI-driven ranging tools. Supplier Negotiation is being augmented by contract intelligence. Space Planning is being automated by planogram software.

The module is used primarily by the Commercial HRBP and L&D lead. The Buying & Merchandising function sunburst shows job families: Category Management, Range & Space Planning, Buying Operations, Digital Commerce, Supplier Management. Clicking into **Category Management** reveals sub-job families: Food Categories, Non-Food Categories, Private Label. The detail view for **Private Label** shows a Sunrise cluster: AI-assisted Product Development, Consumer Trend Intelligence, Sustainability Traceability — all flagged Build (because these are differentiating capabilities the org wants to own internally).

**The decision it typically drives:**
The Head of Buying uses the module to build the annual L&D investment case. The module shows 34 skills flagged Build across the Commercial function, 18 flagged Buy, and 9 flagged Bot. The 9 Bot skills map directly to tools already in the procurement pipeline — this becomes the business case for the automation investment: "These 9 skills will be partially replaced by the tools we're buying. We should redirect the L&D budget from these skills to the 34 Build skills instead."

**Industry-specific nuance:**
Retail has **seasonal workforce peaks** and **high volume at junior grades** (Buyer Admin / Merchandising Admin). The role × skill matrix for junior roles is kept deliberately shallow (fewer skills, Basic and Intermediate only) to keep the onboarding and training planning tractable.

---

### Healthcare & Life Sciences

**Typical entry point:** Head of Talent, HR Director, or L&D Director at a pharma, medtech, or CRO. Enters at Slab 2–4, often triggered by a clinical trial lifecycle change or a regulatory inspection preparation cycle.

**How they use it:**
The pressure in healthcare is compliance and specialisation. Every skill in a GCP (Good Clinical Practice) or GMP (Good Manufacturing Practice) environment has a regulatory implication — someone who is Basic in GDP (Good Distribution Practice) should not be signing off on distribution records. The proficiency ladder in the module becomes a compliance map: Advanced means qualified and signed off; Basic means in training, not yet cleared for independent work.

The Clinical Operations function sunburst shows job families: Clinical Trial Management, Data Management, Regulatory Affairs, Pharmacovigilance, Site Management. Drilling into **Pharmacovigilance** → **Signal Detection** reveals skill clusters including: Medical Literature Review, Signal Detection Methodology, Benefit-Risk Assessment, and — in the Sunrise cluster — AI-assisted Signal Detection, NLP for Adverse Event Processing, Automated Periodic Safety Update Reporting.

**The decision it typically drives:**
Ahead of a MHRA or FDA inspection, the Head of Clinical Compliance uses the module to confirm that all roles in the Pharmacovigilance function are mapped at Advanced for the skills that are inspection-critical. Any role showing Intermediate or Basic on a mandatory skill is flagged for immediate retraining. The Bot-flagged skills (Automated Periodic Safety Reporting, NLP-assisted Literature Review) feed the technology investment roadmap.

**Industry-specific nuance:**
Healthcare skills have **regulatory validity windows** — a GCP certification lapses if not renewed every 2 years. The module captures this conceptually through the skill lifecycle (a certification approaching expiry becomes a "Sunset" signal for the current cohort), but the certification renewal workflow itself is handled by the LMS integration.

---

### Manufacturing & Supply Chain

**Typical entry point:** HR Director, Head of Operations, or L&D Manager at a large manufacturer or 3PL. Enters at Slab 2–3.

**How they use it:**
Manufacturing has a distinct challenge that sets it apart from service industries: skills live on the **shop floor**, not in an office. The gap between what the skills taxonomy says and what the workforce can actually do is large, and the path to closing it (hands-on training, apprenticeships, certification programmes) is slow. The module is used primarily for workforce planning and investment prioritisation — not for day-to-day operations.

The Operations function sunburst shows job families: Production, Quality, Maintenance & Reliability, Process Engineering, Continuous Improvement, Health & Safety. Drilling into **Continuous Improvement** → **Lean Operations** shows skill clusters: Value Stream Mapping, Waste Elimination (8 Wastes), Kaizen Facilitation, Statistical Process Control, and — in the Sunrise cluster — Digital Twin Implementation, AI-assisted OEE Monitoring, Predictive Quality Analytics.

**The decision it typically drives:**
The Plant Director and HR Director use the module to plan the annual apprenticeship intake. The Bot flags across the Production and Quality functions show 8 skills where automation investment is active. These skills are being removed from the apprenticeship curriculum and replaced with Digital Twin and IIoT skills that are Sunrise and flagged Build (because there is no external hiring market yet — the talent has to be grown internally).

**Industry-specific nuance:**
Manufacturing has **trade and craft skills** that do not fit neatly into the Functional / Technical binary. A Maintenance Engineer's skill in Hydraulic Systems is both domain knowledge (Functional) and hands-on capability (Technical). The module handles this by allowing a skill to be classified as Technical with a cluster name that signals the domain context (e.g. cluster: "Mechanical Systems", skill: "Hydraulic Systems Maintenance", type: Technical).

---

### Professional Services

**Typical entry point:** Head of Talent, L&D Director, or People Partner at a consulting firm, law firm, or audit practice. Typically enters at Slab 2–3.

**How they use it:**
Professional services firms are paradoxically the least likely to have a formal skills taxonomy — the product is people, yet the skills map rarely exists outside of annual appraisal forms. The module is used to solve the "we keep losing partners to competitors because we can't articulate a clear development path" problem and the "our AI upskilling is ad hoc" problem.

The Advisory function sunburst shows job families by practice area: Strategy, Operations, Technology, People & Change, Finance Advisory, Risk & Regulatory. Drilling into **Technology Advisory** → **AI & Digital Transformation** shows skill clusters: AI Strategy, Data Architecture, Change Management for Technology, Client Stakeholder Management, and the universal AI & Automation cluster with skills like Prompt Engineering, AI Governance, and Agentic AI.

**The decision it typically drives:**
A Practice Head uses the module to structure the promotion case for 4 Senior Consultants ready for the Manager grade. The module shows exactly what Advanced looks like for each skill the Manager grade requires. This is used as a structured assessment framework in the promotion panel — instead of "we think they're ready," the panel reviews each candidate's profile against the explicit capability requirements in the module.

**Industry-specific nuance:**
Professional services has **client-facing skills** (Client Development, Proposal Writing, Executive Presence) that are distinct from technical delivery skills. These are modelled as Functional skills in a dedicated "Client & Commercial" cluster, and they are universally flagged Build (because they can only be developed through experience, mentoring, and deliberate practice — they cannot be bought or automated).

---

## 4. The Setup Journey (All Configurations)

Regardless of org size or industry, the setup follows the same four phases. Duration scales with org complexity.

### Phase 1 — Taxonomy Discovery (1–4 weeks)

**Goal:** Confirm the function list, job family structure, and grade taxonomy.

The implementation team runs structured workshops with HR and function representatives. They use the industry starter taxonomy as a starting point — this means workshops focus on confirming and adjusting, not starting from blank.

**Key outputs:**
- Confirmed function list (8–30+ functions depending on slab)
- Job family names and hierarchy confirmed per function
- Grade taxonomy agreed (what names, how many levels)
- List of any excluded functions / workbooks (those without proficiency data)

---

### Phase 2 — Skill Authoring (2–8 weeks)

**Goal:** Populate proficiency definitions and BBBB flags for every skill.

This is the heaviest phase. For each skill, the HR or capability team must write (or confirm the pre-built) definitions for Basic, Intermediate, and Advanced. The BBBB flags are set per skill based on the function lead's input.

**Common shortcuts:**
- Use the pre-built industry library for Common skills (AI literacy, data literacy, communication) — saves 30–50% of authoring time
- Prioritise Sunrise skills first — these are the ones leadership cares about most right now
- Use "definition TBC" placeholders for Sunset skills to be deprecated — they don't need full proficiency ladders

**Key output:** Validated taxonomy in the admin Excel template (Slabs 1–3) or via the API (Slabs 4–5).

---

### Phase 3 — Configuration & Load (1–2 weeks)

**Goal:** Get the taxonomy into the module and validate the rendering.

The validated taxonomy is imported. The implementation team runs a structured walkthrough:
1. Does every function card appear on the landing page?
2. Does the sunburst render correctly for each function?
3. Are there any skills with missing proficiency definitions?
4. Are the BBBB flags rendering correctly?
5. Does the role × skill matrix align (role count matches role_levels array length)?

Any data quality issues are resolved before sign-off.

---

### Phase 4 — Stakeholder Walkthrough & Handover (1 week)

**Goal:** Ensure leadership and HR can use the module independently.

A 2-hour walkthrough is run with:
- The CHRO / CPO (strategic use: landing → sunburst → stat strip)
- 1–2 HRBPs (operational use: skill detail → proficiency ladder → matrix)
- The L&D lead (investment use: BBBB flags → skill category → Build identification)

The module is handed over with a one-page user guide per persona. Ongoing support is provided through a shared success channel.

---

## 5. Day-in-the-Life Scenarios

Three concrete usage scenarios that cut across slab and industry.

---

### Scenario A — "We need to hire 3 people for our new AI team"

**User:** Head of Talent, 1,800-person healthcare company (Slab 2)

1. Opens the module, navigates to **Technology & Digital** → **Data & AI** → **AI Enablement**
2. The detail view shows the skill clusters for the AI team roles: AI Strategy, ML Engineering, Data Engineering, AI Governance, AI & Automation
3. She clicks **ML Engineering** and reviews the skill card for **LLM Fine-tuning** — it is flagged Sunrise + Buy (external hire) + Build (long-term internal development target)
4. She uses the "Show role proficiency" toggle to see which of the 3 new roles require Advanced in LLM Fine-tuning vs. Intermediate
5. She walks away with: a list of required Advanced skills for each role (which becomes the JD must-haves), a list of Intermediate skills (nice-to-haves), and the explicit Advanced-level definitions she can use to write the job posting in plain language

**Time taken:** 12 minutes. No meeting required.

---

### Scenario B — "The board wants to know if we're AI-ready"

**User:** CHRO, 6,000-person retail company (Slab 4)

1. Opens the module. Landing page shows 22 functions, 1,480 skills
2. She mentally filters for the Sunrise signal: the stat strip shows that 31% of all skills are flagged Sunrise across the org
3. She navigates to 3 functions she suspects are lagging: Finance, Procurement, Legal
4. In each function sunburst, she clicks into the AI & Automation cluster for each function's primary sub-job family
5. She sees the same 11 universal AI skills in each cluster — but the role × skill matrix tells a different story. Finance has 78% of Analyst-grade roles at Basic in AI Literacy. Procurement has 65% at Basic. Legal has 89% at Basic
6. She builds a 3-slide board narrative in 30 minutes: current AI skill concentration (Tech and Data functions), current gap in business functions (Finance, Procurement, Legal), and the Build investment required to close it

**Time taken:** 30 minutes. The board narrative is evidence-based, not anecdotal.

---

### Scenario C — "I want to move from Finance to Strategy"

**User:** Senior Financial Analyst, 12,000-person professional services firm (Slab 5, employee self-service view)

1. Opens the module in employee self-service mode. She sees her current function: Finance → Financial Planning & Analysis → Commercial FP&A
2. She reviews her current role (Senior Financial Analyst, G12) against the skill requirements shown in the matrix. She can see she is mapped at Advanced in Financial Modelling, Advanced in Commercial Analysis, Intermediate in Business Partnering
3. She navigates to the target function: Strategy → Corporate Strategy → Strategy & Business Development
4. She reviews the Strategy Analyst role (also G12). The skill requirements are: Advanced in Structured Problem Solving, Advanced in Hypothesis-driven Analysis, Intermediate in Financial Modelling, Basic in Stakeholder Management
5. She identifies the overlap (Financial Modelling: she has Advanced, target needs Intermediate — she exceeds it) and the gap (Structured Problem Solving: she has no current mapping — she needs to develop this to at least Intermediate before applying)
6. She shares the gap with her manager and agrees a development plan: join the internal consulting project secondment programme to build Structured Problem Solving in a live context (Build)

**Time taken:** 20 minutes. The conversation with her manager is specific and plannable.

---

*For technical specifications supporting each scenario, refer to `product-analysis.md`.*
