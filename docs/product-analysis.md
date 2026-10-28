# Executive Insights Module — Product Specification

> **Module:** Executive Insights (Capability Playbook)
> **Parent Product:** WorkRedesign Platform
> **Version:** 1.0 (Pre-GA)
> **Last updated:** 2026-10-28
> **Status:** Prototype validated; preparing for multi-tenant, multi-industry release

---

## Table of Contents

1. [What This Module Does](#1-what-this-module-does)
2. [User Personas & Access Tiers](#2-user-personas--access-tiers)
3. [Universal Data Model](#3-universal-data-model)
4. [Configuration by Organisation Size](#4-configuration-by-organisation-size)
5. [Industry Adaptation Patterns](#5-industry-adaptation-patterns)
6. [Platform-Fixed Behaviour (All Configurations)](#6-platform-fixed-behaviour-all-configurations)
7. [UI & Component Specifications](#7-ui--component-specifications)
8. [Data Architecture & Integration Patterns](#8-data-architecture--integration-patterns)
9. [Known Gaps & Product Decisions Required](#9-known-gaps--product-decisions-required)

---

## 1. What This Module Does

The **Executive Insights** module gives leadership teams a unified, navigable view of their organisation's capability landscape — what skills exist, where they sit, how they are distributed across functions and roles, and where the gaps are.

It answers three questions leadership cannot easily answer from an HRIS or LMS alone:

| Question | How the module answers it |
|---|---|
| **What capabilities do we have?** | Function → Job Family → Sub-Job Family → Skill hierarchy rendered as an interactive sunburst chart |
| **How deep is each capability?** | Three-tier proficiency ladder (Basic / Intermediate / Advanced) with explicit definitions per skill per level |
| **Where should we invest?** | Build / Buy / Borrow / Bot sourcing flags + skill lifecycle status (Core / Sunrise / Sunset) on every skill |

The module is **read-only for leadership**. It is a consumption surface, not an authoring tool. Data enters via integration (see §8) or via an admin configuration interface that is out of scope for this specification.

---

## 2. User Personas & Access Tiers

These personas are consistent across all org sizes and industries. What changes is the *data scope* available to each persona (see §4).

| Persona | Primary Goal | Key UI Surfaces |
|---|---|---|
| **C-Suite / Board** | Org-wide capability health at a glance | Landing stat strip, function cards, top-level sunburst |
| **Function Head / VP** | Understand capability depth in their function | Function sunburst (zoomed), per-function stat strip, list panel |
| **HR / People Director** | Plan L&D investment, identify Build vs Buy | Skill detail view, proficiency ladder, BBBB badges |
| **Talent Partner / Skills Planner** | Role-level gap analysis | Role × Skill matrix, skill search, per-role proficiency toggle |
| **Workforce Analyst** | Cross-function skill coverage | Global search index, cluster-level aggregations |

---

## 3. Universal Data Model

The following schema is **industry-agnostic**. Industry-specific implementations populate the same structure with domain-relevant content (see §5).

### Hierarchy

```
Organisation
  └── Function (e.g. Finance, Engineering, Sales)
        └── Job Family (e.g. Financial Planning, Backend Engineering)
              └── Sub-Job Family (e.g. FP&A, Platform Engineering)
                    ├── Roles [ { role_name, grade } ]
                    └── Clusters [ { cluster_name, skills[] } ]
                          └── Skill { ... }
```

### Entity Schemas

#### `Function`
| Field | Type | Notes |
|---|---|---|
| `id` | string | Unique within tenant |
| `name` | string | Display name |
| `job_families` | `JobFamily[]` | Min 1 |

#### `JobFamily`
| Field | Type | Notes |
|---|---|---|
| `name` | string | |
| `sub_job_families` | `SubJobFamily[]` | May be empty for flat orgs (see §4) |

#### `SubJobFamily`
| Field | Type | Notes |
|---|---|---|
| `name` | string | |
| `roles` | `Role[]` | Ordered — order determines `role_levels` alignment |
| `clusters` | `Cluster[]` | |

#### `Role`
| Field | Type | Notes |
|---|---|---|
| `role` | string | Display name |
| `grade` | string | Grade label — see §4 for per-slab grade taxonomies |

#### `Cluster`
| Field | Type | Notes |
|---|---|---|
| `name` | string | Thematic grouping within a sub-job family |
| `skills` | `Skill[]` | |

#### `Skill`
| Field | Type | Notes |
|---|---|---|
| `skill` | string | Canonical skill name |
| `type` | `"Functional" \| "Technical"` | |
| `category` | `"Core" \| "Sunrise" \| "Sunset"` | Lifecycle stage |
| `definition` | string | General definition |
| `basic_def` | string | What Basic looks like in practice |
| `intermediate_def` | string | What Intermediate looks like in practice |
| `advanced_def` | string | What Advanced looks like in practice |
| `build` | boolean | Develop internally via L&D |
| `buy` | boolean | Hire for this skill externally |
| `borrow` | boolean | Engage contractors / partners |
| `bot` | boolean | Candidate for automation |
| `role_levels` | `("Basic"\|"Intermediate"\|"Advanced"\|null)[]` | Indexed to `SubJobFamily.roles` in order; null = skill not required for that role |

### Universal Skill Clusters (Cross-Industry)

The following clusters appear in every industry configuration. They are pre-seeded and require only localisation of skill names.

| Cluster | Category | Rationale |
|---|---|---|
| **AI & Automation** | Sunrise | Universal across all functions and industries |
| **Leadership & People Management** | Core | Applies to all senior roles |
| **Communication & Influence** | Core | Applies to all client-facing and leadership roles |
| **Data Literacy & Analytics** | Core / Sunrise | Foundational for analyst and manager grades |

---

## 4. Configuration by Organisation Size

Org size determines the **depth of hierarchy**, **grade nomenclature**, **number of functions**, and **default data packaging**. The table below defines the configuration baseline for each headcount slab.

---

### Slab 1 — 500 to 1,000 Employees

**Profile:** High-growth scale-up or established SME. Functions exist but are not deeply specialised. A single head of HR/People typically owns capability planning without a dedicated workforce analytics team.

| Configuration Dimension | Setting |
|---|---|
| **Hierarchy depth** | 3 levels: Function → Job Family → Skill Cluster (Sub-Job Family layer collapsed or omitted) |
| **Max functions** | 6–10 |
| **Max job families per function** | 3–5 |
| **Grade nomenclature** | Flat: Individual Contributor / Senior IC / Lead / Manager / Senior Manager / Director |
| **Sunburst complexity** | Low — 2-tier chart (Function ring + Job Family ring) |
| **Role × Skill matrix** | Simplified: up to 8 roles × 20 skills per capability |
| **Proficiency system** | Full 3-tier (Basic / Intermediate / Advanced) retained |
| **BBBB sourcing flags** | Retained; Build and Buy most relevant at this size |
| **Default data package** | Starter taxonomy: ~6 functions × 4 job families × 2 clusters × 15 skills ≈ 720 skills total |
| **Primary persona** | Founder / COO / Head of People |
| **Excluded feature** | Sub-Job Family layer; CrossFunction aggregation stat strip |

**Grade Taxonomy Example:**
```
IC (L1) → Senior IC (L2) → Lead (L3) → Manager (M1) → Senior Manager (M2) → Director (D1)
```

---

### Slab 2 — 1,000 to 2,500 Employees

**Profile:** Mid-market company or a specialist BPO / GCC with one primary client. Functions are well-defined; job families exist but sub-specialisation is limited. An HR Business Partner or L&D team drives capability planning.

| Configuration Dimension | Setting |
|---|---|
| **Hierarchy depth** | 4 levels: Function → Job Family → Sub-Job Family → Skill Cluster |
| **Max functions** | 10–14 |
| **Max job families per function** | 4–7 |
| **Grade nomenclature** | Two-tier: Associate / Senior Associate / Lead / Manager / Senior Manager / Head / Director |
| **Sunburst complexity** | Medium — 3-tier chart (Function ring + Job Family ring + Sub-Job Family ring) |
| **Role × Skill matrix** | Standard: up to 15 roles × 40 skills per capability |
| **Proficiency system** | Full 3-tier |
| **BBBB sourcing flags** | All four active; Borrow significant (contracting common at this size) |
| **Default data package** | Standard taxonomy: ~12 functions × 5 job families × 3 clusters × 18 skills ≈ 3,240 skills total |
| **Primary persona** | CPO / CHRO / Function VP |
| **New at this slab** | Sub-Job Family layer enabled; per-function stat strip |

**Grade Taxonomy Example:**
```
Associate (G1) → Senior Associate (G2) → Lead (G3) → Manager (G4) → Senior Manager (G5) → Head (G6) → Director (G7)
```

---

### Slab 3 — 2,500 to 5,000 Employees

**Profile:** Enterprise division or mid-scale GCC. Multiple business units sharing a centre. Dedicated workforce planning teams exist. Function heads have autonomy over capability investment within budget.

| Configuration Dimension | Setting |
|---|---|
| **Hierarchy depth** | Full 4-level hierarchy; Sub-Job Families deeply populated |
| **Max functions** | 14–18 |
| **Max job families per function** | 5–9 |
| **Grade nomenclature** | Work Level or Band framework: WL1 (Associate / Senior Associate / Lead) → WL2 (Manager / Senior Manager) → WL3 (Head / Associate Director) → WL4 (Director) |
| **Sunburst complexity** | High — full 3-tier chart with per-sub-job-family coloring |
| **Role × Skill matrix** | Full: up to 25 roles × 60 skills per capability |
| **Proficiency system** | Full 3-tier with per-role levels |
| **BBBB sourcing flags** | All four; Bot flag gains significance (automation investment active) |
| **Default data package** | Extended taxonomy: ~16 functions × 7 job families × 4 clusters × 22 skills ≈ 9,856 skills total |
| **Primary persona** | CHRO, HRBP, Workforce Analytics Lead |
| **New at this slab** | Multiple business-unit workbooks; excluded-workbook banner for incomplete data; CrossWalker / rotation role type |

**Grade Taxonomy Example:**
```
WL1-Associate → WL1-Senior Associate → WL1-Lead → WL2-Manager → WL2-Senior Manager → WL3-Head → WL3-Associate Director → WL4-Director
```

---

### Slab 4 — 5,000 to 10,000 Employees

**Profile:** Large enterprise or multi-client GCC. Multiple geographies. HR CoE (Centre of Excellence) with dedicated skills taxonomy teams. Capability data often owned by L&D and Talent Acquisition separately.

| Configuration Dimension | Setting |
|---|---|
| **Hierarchy depth** | Full hierarchy; multiple Sub-Job Families per Job Family expected |
| **Max functions** | 18–24 |
| **Max job families per function** | 6–12 |
| **Grade nomenclature** | Global grade band (e.g. G10–G15) or Work Level framework aligned to compensation bands |
| **Sunburst complexity** | High; color palette expansion to 12+ colors required |
| **Role × Skill matrix** | Extended: up to 40 roles × 80 skills per capability; horizontal scroll on matrix |
| **Proficiency system** | Full 3-tier; potentially extended to 4-tier (Awareness / Basic / Intermediate / Advanced) |
| **BBBB sourcing flags** | All four; Bot is strategic investment signal |
| **Default data package** | Enterprise taxonomy: ~22 functions × 10 job families × 5 clusters × 25 skills ≈ 27,500 skills total |
| **Primary persona** | CHRO, CPO, Head of Talent, Capability COE |
| **New at this slab** | Geography/location filter on stat strip; multi-workbook federation; RBAC by function |

**Grade Taxonomy Example:**
```
G10 (Analyst) → G11 (Senior Analyst) → G12 (Specialist) → G13 (Senior Specialist / Manager) → G14 (Senior Manager / Principal) → G15 (Director / VP)
```

---

### Slab 5 — 10,000+ Employees

**Profile:** Global enterprise, large BPO, or multi-geography GCC. Skills taxonomy is a strategic asset. Dedicated people analytics function. Integration with HRIS (Workday, SAP SuccessFactors), LMS, and talent marketplace required.

| Configuration Dimension | Setting |
|---|---|
| **Hierarchy depth** | Full hierarchy; may require a 5th level (Specialisation within Sub-Job Family) |
| **Max functions** | 24+ |
| **Max job families per function** | Uncapped; pagination or function-level search required |
| **Grade nomenclature** | Global Job Architecture (GJA) with Career Level + Job Family Group; Hay / Mercer / Willis Towers Watson band mapping |
| **Sunburst complexity** | High; lazy-loading of sub-levels; optional treemap view for very large functions |
| **Role × Skill matrix** | Paginated; column-freeze on role names; sortable by coverage % |
| **Proficiency system** | Full 3-tier at minimum; optionally 5-tier (Awareness → Basic → Proficient → Advanced → Expert) |
| **BBBB sourcing flags** | All four; sourcing decisions versioned and timestamped |
| **Default data package** | Full enterprise library: 30+ functions; custom taxonomy builder for non-standard functions |
| **Primary persona** | Chief People Officer, Group Head of Talent, People Analytics Director |
| **New at this slab** | API-first data layer (no embedded JSON); multi-tenant isolation; skill versioning; live integration with talent marketplace |

**Grade Taxonomy Example:**
```
Career Level 1–2 (Individual Contributor) → CL3–4 (Senior IC / Specialist) → CL5–6 (Manager / Senior Manager) → CL7–8 (Director / VP) → CL9–10 (SVP / EVP / C-Suite)
```

---

## 5. Industry Adaptation Patterns

The universal data model (§3) remains constant. What changes per industry is the **function taxonomy**, **skill cluster vocabulary**, **grade nomenclature conventions**, and the **predominant sourcing strategy** (Build vs Buy vs Borrow vs Bot mix).

The AI & Automation cluster, Leadership, Communication, and Data Literacy clusters remain universal across all industries.

---

### Technology & Software Product

| Dimension | Configuration |
|---|---|
| **Core functions** | Engineering, Product, Design, Data & AI, Security, DevOps/Platform, Sales, Customer Success, Finance, Legal & Compliance |
| **Signature skill clusters** | Full-Stack Development, Cloud & Infrastructure, ML Engineering, Product Discovery, UX Research, Security Architecture, Site Reliability |
| **Grade norm** | Engineering career ladder (IC1–IC7 / Staff / Principal / Distinguished) + Management track (EM / Director of Engineering / VP Engineering / CTO) |
| **BBBB mix** | Heavy Build (engineering talent); selective Buy (specialist ML, security); high Bot (CI/CD, test automation) |
| **Sunrise priorities** | AI Agents, LLM Fine-tuning, Platform Engineering, Developer Experience |
| **Sunset watch** | Legacy monolith skills, on-premise infrastructure, manual QA |
| **Slab relevance** | All slabs; product companies typically enter at Slab 1–2 and scale to Slab 4–5 |

---

### Financial Services & Banking

| Dimension | Configuration |
|---|---|
| **Core functions** | Retail Banking, Corporate & Investment Banking, Risk, Compliance & Regulatory Affairs, Finance & Treasury, Technology & Digital, Operations, HR, Legal |
| **Signature skill clusters** | Credit Risk Modelling, Regulatory Reporting (Basel IV, IFRS 9), AML & Financial Crime, Trade Finance, Wealth Management, Core Banking Technology |
| **Grade norm** | Analyst → Associate → Vice President → Director → Managing Director (IBD-style); or Grade-band frameworks (GS01–GS12) in ops/tech |
| **BBBB mix** | Mix of Build (regulatory/compliance skills — hard to hire) and Buy (quantitative finance, AI/ML risk); Borrow for project delivery; Bot for reconciliation and reporting |
| **Sunrise priorities** | RegTech automation, AI credit decisioning, open banking APIs, ESG data & reporting |
| **Sunset watch** | Manual reconciliation, LIBOR-adjacent skills, legacy core banking platforms |
| **Slab relevance** | Slabs 3–5; financial services rarely builds a formal capability playbook below 2,500 employees |

---

### Retail & Consumer Goods

| Dimension | Configuration |
|---|---|
| **Core functions** | Commercial / Buying & Merchandising, Supply Chain & Logistics, Digital Commerce, Marketing, Finance, HR, Technology, Property & Facilities |
| **Signature skill clusters** | Range Planning, Supplier Negotiation, Category Management, Demand Forecasting, Space Planning, Digital Shelf Optimisation, Performance Marketing |
| **Grade norm** | Buying / Merchandising ladder (Buyer Admin → Buyer → Senior Buyer → Buying Manager → Head of Category → Director); Finance uses standard WL framework |
| **BBBB mix** | Build for buying/merchandising domain skills; Buy for data science and digital commerce; Borrow for seasonal/project logistics; Bot for PO processing, shelf analytics |
| **Sunrise priorities** | AI-assisted ranging, dynamic pricing, personalisation at scale, sustainability traceability |
| **Sunset watch** | Manual order management, paper-based supplier onboarding |
| **Slab relevance** | Slabs 2–5; a 500-person retailer is typically a pure merchant with limited functional depth |

---

### Healthcare & Life Sciences

| Dimension | Configuration |
|---|---|
| **Core functions** | Clinical Operations, Regulatory Affairs, R&D / Drug Discovery, Medical Affairs, Pharmacovigilance, Commercial (Market Access, Sales), Quality Assurance, Finance, HR |
| **Signature skill clusters** | GCP / GMP Compliance, Clinical Trial Management, Regulatory Submissions (FDA, EMA), Biostatistics, Real World Evidence, Health Economics & Outcomes Research |
| **Grade norm** | Scientific ladder (Associate Scientist → Scientist → Senior Scientist → Principal Scientist → Fellow); Management ladder (Manager → Director → VP → SVP); Regulatory specialists tracked separately |
| **BBBB mix** | Build for scientific/clinical domain expertise; Buy for specialised regulatory affairs, bioinformatics; Borrow for CRO/CMO project work; Bot for regulatory document processing, pharmacovigilance signal detection |
| **Sunrise priorities** | AI-assisted drug discovery, digital biomarkers, decentralised clinical trials, RWE analytics |
| **Sunset watch** | Paper-based regulatory submissions, legacy CTMS platforms |
| **Slab relevance** | Slabs 2–5; pharma/biotech frequently deploys playbooks at the 1,000+ headcount mark |

---

### Manufacturing & Supply Chain

| Dimension | Configuration |
|---|---|
| **Core functions** | Operations / Plant Management, Supply Chain (Planning, Procurement, Logistics), Engineering, Quality, Finance, HR, Sales & Customer Service, Technology / Industry 4.0 |
| **Signature skill clusters** | Lean / Six Sigma, Production Planning (MRP/ERP), Supplier Quality Management, Process Engineering, Industrial Automation / SCADA, Reliability Engineering |
| **Grade norm** | Technical ladder (Technician → Engineer → Senior Engineer → Principal Engineer → Technical Director); Operations ladder (Supervisor → Plant Manager → Operations Director) |
| **BBBB mix** | Build for operations and engineering; Buy for digital/Industry 4.0 skills; Borrow for project engineering; Bot for quality inspection, predictive maintenance |
| **Sunrise priorities** | Digital Twin, IIoT, Predictive Quality, AI-driven demand planning, autonomous logistics |
| **Sunset watch** | Manual paper-based production records, legacy PLC programming, on-premise ERP customisation |
| **Slab relevance** | Slabs 2–5; a Slab 1 manufacturer typically has limited function specialisation |

---

### Professional Services (Consulting, Audit, Legal)

| Dimension | Configuration |
|---|---|
| **Core functions** | Advisory / Consulting, Audit & Assurance, Tax, Technology Consulting, People & Organisation, Finance, HR, Business Development, Knowledge Management |
| **Signature skill clusters** | Engagement Management, Client Development, Structured Problem Solving, Financial Modelling, Change Management, Industry Domain Knowledge (varies by practice) |
| **Grade norm** | Professional services pyramid: Analyst / Associate → Consultant / Senior Consultant → Manager → Senior Manager → Director → Partner / Principal |
| **BBBB mix** | Build for client-specific domain skills; Buy for specialised technology/data talent; Borrow uncommon (contractor model is firm-dependent); Bot for document review, data extraction, report generation |
| **Sunrise priorities** | GenAI for document synthesis, AI-assisted audit, legal contract intelligence, client-facing digital tools |
| **Sunset watch** | Manual workpaper, legacy audit software, non-AI research workflows |
| **Slab relevance** | Slabs 2–4; large global firms at Slab 5 require region/practice unit segmentation |

---

## 6. Platform-Fixed Behaviour (All Configurations)

The following behaviours are identical regardless of org size or industry. They form the non-negotiable UX and data contract of the module.

### Navigation Model

```
Landing (Function Cards)
  └─[Click function]──► Sunburst View (function root)
        └─[Click job-family wedge]──► Sunburst (zoomed to job family)
              └─[Click capability leaf]──► Detail View
                    └─[Back button / breadcrumb]──► Sunburst View
  └─[Search result click]──────────────────────────► Detail View (animated jump)
  └─[Logo / brand click]──────────────────────────► Landing
```

### Skill Lifecycle Model

| Category | Meaning | UI Treatment |
|---|---|---|
| **Core** | Established, required skill | Teal chip |
| **Sunrise** | Emerging / strategic — invest now | Amber chip |
| **Sunset** | Deprecating — wind down training | Rose chip |

### Sourcing Decision Flags (BBBB)

| Flag | Icon | Meaning |
|---|---|---|
| **Build** | 🛠️ | Develop internally through L&D |
| **Buy** | 🛒 | Hire externally for this skill |
| **Borrow** | 🤝 | Use contractors / partners |
| **Bot** | 🤖 | Automate this capability |

Flags are not mutually exclusive — a skill may be Build AND Bot (develop partial capability internally, automate routine execution).

### Proficiency Ladder

Three levels are mandatory. Optional: a 4th level ("Awareness" below Basic) for Slab 4–5 only.

| Level | Visual | Meaning |
|---|---|---|
| **Basic** | Light shade (80%) | Awareness and foundational application |
| **Intermediate** | Mid shade (45%) | Independent, consistent execution |
| **Advanced** | Full color | Expert, coaches others, shapes strategy |
| **null / empty** | No cell | Skill not required for this role |

### Search Behaviour

- Triggered on ≥ 2 characters
- Matches on skill name only (current implementation)
- Returns ≤ 10 results showing skill name + function + job family + capability path
- Selection triggers animated 3-phase navigation: `enterFunction` (0 ms) → zoom to job family (30 ms delay) → `openDetail` (750 ms delay)
- **Planned:** extend matching to cluster names, capability definitions, and role names

---

## 7. UI & Component Specifications

### Landing View

| Component | Description | Configures by slab? |
|---|---|---|
| **Org-wide stat strip** | KPI tiles: Total Functions, Job Families, Capabilities, Distinct Skills, Role Instances | Tile count fixed; values change with data |
| **Excluded-workbook banner** | Amber warning when one or more functions lack proficiency data | Optional; shown only when exclusion flag set |
| **Function card grid** | One card per function; SVG activity rings showing job-family breakdown | Ring count = job family count per function |
| **Activity rings** | Concentric SVG rings on each function card: outer = capability coverage %, inner = skill breadth % | Calculated on render |

### Sunburst View

| Component | Description | Notes |
|---|---|---|
| **D3 Sunburst (900×900 SVG)** | Zoomable; 700ms cubic in-out transition | D3 v7.9.0; `d3.partition()` + `d3.arc()` |
| **Center label + hit circle** | Shows focused node name; click = zoom out | Hit circle doubles as back target |
| **Side list panel** | Text navigation companion; updates with zoom level | Chevron `›` per item; click = zoom or open detail |
| **Per-function stat strip** | Same KPI tile format as landing; scoped to active function | Rendered by `renderFuncStats()` |
| **Back float button** | Overlay button; appears only when not at function root | Label = parent node name |
| **Breadcrumb bar** | "All functions / [Function name]" trail | Sticky below page header |

### Detail View

| Component | Description | Notes |
|---|---|---|
| **Capability header** | Tag (parent job family) + title (sub-job family) + definition | Static display |
| **Role chips** | All roles in this capability with grade badge | Display only |
| **Sourcing summary strip** | Count of skills by BBBB type | Computed from skill flags |
| **Collapsible skill clusters** | One section per cluster; toggle open/close | Click cluster header |
| **Skill card** | Skill name, type badge, category badge, BBBB badges, proficiency ladder, "Show role proficiency" toggle | Toggle shows inline per-role table |
| **Skill × Role matrix** | Grid: rows = skills, columns = roles; cells color-coded by proficiency level | Horizontally scrollable at Slab 4+ |

### Color System

| Use | Value |
|---|---|
| Primary brand | `--teal-500: #009480` |
| Advanced proficiency | Full job-family palette color |
| Intermediate proficiency | 45% shade toward white |
| Basic proficiency | 80% shade toward white |
| Sunrise chip | `--amber: #F59E0B` |
| Core chip | `--teal-500` |
| Sunset chip | `--rose: #F43F5E` |
| Build badge | `#009480` |
| Buy badge | `#F59E0B` |
| Borrow badge | `#7C3AED` |
| Bot badge | `#F43F5E` |
| Surface (light) | `--bg: #F4F6F8` |
| Surface (dark toggle) | `--bg: #0D1117` |

---

## 8. Data Architecture & Integration Patterns

### Current State (Prototype)

All data is embedded as inline JSON (`capdata`) in the HTML file. ~1.85MB for the TBS reference dataset (16 functions). No API calls. No backend. Suitable only for a static prototype shared as a single file.

### Target State by Slab

| Slab | Data Delivery Pattern | Notes |
|---|---|---|
| **500–1,000** | Static JSON file loaded from same origin | Replace inline embed with `fetch('./data/capdata.json')` on init |
| **1,000–2,500** | Static JSON or lightweight REST endpoint | Single-function lazy load on `enterFunction()` |
| **2,500–5,000** | REST API per function; paginated detail | `GET /api/v1/functions` → `GET /api/v1/functions/:id/capabilities` |
| **5,000–10,000** | REST API with RBAC; function-scoped tokens | Persona-gated data; function heads see only their function |
| **10,000+** | GraphQL or federated REST; real-time sync | Webhooks for skill data updates from HRIS/LMS; versioned capability snapshots |

### Integration Points

| System | Integration Type | Data Direction | Priority |
|---|---|---|---|
| **HRIS (Workday, SAP SF, Oracle HCM)** | REST / SOAP export | Role + grade data → Playbook | High |
| **LMS (Cornerstone, Degreed, LinkedIn Learning)** | REST | Proficiency assessments → Playbook | High |
| **Talent Marketplace (Beamery, Eightfold, Fuel50)** | Bidirectional REST | Skill tags sync | Medium |
| **Org Chart / Directory** | CSV / REST | Function hierarchy → Playbook | Medium |
| **Skills Authoring (internal HR admin)** | Admin UI (out of scope) | Skill definitions + BBBB flags | Medium |
| **BI Platform (Power BI, Tableau, Looker)** | Data export / embedded | Playbook data → Dashboards | Low |

---

## 9. Known Gaps & Product Decisions Required

### Functional Gaps

| Gap | Impact | Recommended Resolution |
|---|---|---|
| **No role-centric entry point** | Users cannot find "all skills for role X" | Add role search / role profile view; priority for Slab 2+ |
| **Matrix is display-only** | No sorting, filtering, or export on the skill × role grid | Add column sort, filter by category/type, CSV export |
| **No mobile / responsive layout** | Sunburst SVG is fixed 900×900 | Responsive SVG with `viewBox` and `preserveAspectRatio`; or replace with treemap on mobile |
| **Search matches skill name only** | Misses cluster names, definitions, capability names | Extend `buildSearchIndex` to include these fields |
| **No print / export** | No PDF or Excel output | Add print stylesheet + server-side PDF render for Slab 3+ |
| **No shareable deep link** | Search result navigation uses in-memory state; URL never updates | Add URL hash routing (`#/functions/3/capabilities/7`) |
| **`skipAnim` parameter unused** | Animation cannot be suppressed programmatically | Implement or remove; needed for keyboard navigation |

### Data Quality Gates

| Gate | Trigger | Behaviour |
|---|---|---|
| **Incomplete proficiency data** | A function has skills with no `role_levels` populated | Show excluded-workbook banner; name the function explicitly |
| **Empty cluster** | A cluster exists but has 0 skills | Hide cluster section in detail view; flag in admin |
| **Role count mismatch** | `role_levels` array length ≠ `roles` array length | Surface as a data validation error in admin; do not render matrix row |
| **Palette overflow** | A function has more job families than palette colors (current limit: 9) | Extend palette to 14 colors; cycle with distinguishable pattern beyond that |

### Architecture Decisions to Confirm

| # | Decision | Options | Recommended |
|---|---|---|---|
| 1 | **Data delivery for Slab 1** | Inline JSON vs. static file fetch | Static file fetch — keeps HTML cacheable and data replaceable without redeployment |
| 2 | **Proficiency tier extension** | 3-tier (current) vs. 4-tier (Awareness added) | Keep 3-tier for Slabs 1–3; offer 4-tier as config option for Slab 4–5 |
| 3 | **Multi-tenant isolation** | Subdomain per tenant vs. path-based routing vs. single-page multi-org | Subdomain per tenant for Slab 3+; path-based for 1–2 |
| 4 | **RBAC model** | Role-based (HR Admin / Function Lead / Employee) vs. attribute-based | Role-based for Slabs 1–3; ABAC for Slab 4–5 |
| 5 | **Skill versioning** | No versioning (current) vs. snapshot per quarter vs. immutable history | Quarterly snapshot for Slab 3+; immutable history for Slab 5 |
| 6 | **Authoring interface** | In-product admin UI vs. Excel import vs. API-only | Excel import for Slabs 1–2; Admin UI for Slabs 3–5 |
| 7 | **Grade taxonomy** | Free-text grade per org vs. mapped to canonical ladder | Free-text stored; canonical mapping in admin for cross-org benchmarking |
