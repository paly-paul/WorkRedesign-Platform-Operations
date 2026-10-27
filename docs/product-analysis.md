# TBS GCC Capability Playbook — Product & Technical Analysis

> **Source file:** `HTML Dashboard/TBS GCC Capability Playbook (Copy).html`
> **Analysis date:** 2026-10-27
> **Analyst:** Claude Code (automated technical analysis)

---

## 1. Business Logic

### What Problem Does This Solve and For Whom?

The TBS GCC Capability Playbook is a **skills intelligence and workforce planning tool** for **Tesco Business Solutions (TBS)**, targeting its Global Capability Centre (GCC). It consolidates the skills framework for every function in the GCC into a single navigable interface.

**Primary audience:** GCC leadership, HR/talent business partners, function heads, and workforce planning teams. The footer explicitly labels it a "Prototype for leadership review."

**Core problem solved:** Fragmented skills data across multiple spreadsheets/workbooks. The playbook provides a unified, interactive view of:
- What job families and capabilities exist in each function
- What skills are required at each proficiency level per role
- How roles are graded (Work Level 1–4)
- Whether each skill should be built internally, bought externally, borrowed (contracted), or automated (bot)
- Which skills are emerging ("Sunrise") vs. established ("Core") vs. deprecated ("Sunset")

### Core Business Rules

| Rule | Detail |
|---|---|
| Proficiency ladder | Three levels: **Basic → Intermediate → Advanced**. Each skill has a distinct definition per level. |
| Skill lifecycle | Three categories: **Core** (standard), **Sunrise** (emerging/strategic, currently AI-related), **Sunset** (being deprecated — e.g. "Purchase Order Management"). |
| Sourcing decision | Every skill carries four boolean flags: **Build** (develop internally), **Buy** (hire for), **Borrow** (contract), **Bot** (automate). These are not mutually exclusive. |
| Skill type | Two types: **Functional** (domain/process skills) and **Technical** (tool/platform skills). |
| Role × skill matrix | Each skill's `role_levels` array maps to every role in the sub-job family in order, with null meaning the skill is not required for that role. |
| Grade structure | **WL 1 (Associate)**, **WL 1 (Senior Associate)**, **WL 1 (Lead)**, **WL 2 (Manager)**, **WL 2 (Senior Manager)**, **WL 3 (Head)**, **WL 3 (Associate Director)**, **WL 4 (Director)**. |
| AI skills universal | All AI/ML Sunrise skills (AI Literacy, Prompt Engineering, MS Copilot, etc.) appear as a standard cluster across every function and job family. |

### Decision Points Embedded in the UI

- **Build/Buy/Borrow/Bot badges** surface the strategic make-vs-buy signal per skill, supporting headcount and L&D investment decisions.
- **Proficiency level per role** drives gap analysis: if a role shows "Basic" for a skill, targeted training can be planned.
- **Sunrise/Core/Sunset category** indicates where new investment should go vs. where skills are being phased out.
- **Excluded workbook note** (`EXCLUDED_NOTE` banner): at least one business unit's workbook was omitted from the playbook because it lacks proficiency ratings — an explicit data quality gate.

### Domain-Specific Constraints

- The taxonomy is strictly hierarchical: **Function → Job Family → Sub-Job Family → Role** and **Function → Job Family → Sub-Job Family → Cluster → Skill**.
- A role belongs to exactly one sub-job family.
- Skills are grouped into clusters within a sub-job family; a skill cannot span clusters.
- The "CrossWalkers" role type (e.g. "CrossWalkers associate (C&P)") appears to represent employees rotating across functions — a Tesco-specific talent mobility programme.

---

## 2. User Journey

### Implied User Roles / Personas

| Persona | Signals in UI |
|---|---|
| **GCC Leadership / Executive** | Landing page stat strip (org-wide counts); function-level sunburst for strategic overview; "Prototype for leadership review" footer label |
| **HR / Talent Business Partner** | Detailed role × skill matrix; proficiency levels per role; Build/Buy/Borrow/Bot sourcing flags; skill definitions for JD writing and L&D planning |
| **Function Head / Capability Owner** | Drill-down to their own function's job families, capabilities, and skill clusters |
| **Workforce Analyst / Skills Planner** | Skill search index; cluster-level drill-down; capability counts and role instance counts |

### End-to-End Journey: Executive (Strategic Overview)

1. **Landing page** — sees org-wide stat strip: total functions, job families, capabilities, distinct skills, role instances.
2. Scans **function cards** with conic-gradient rings showing the job-family split by capability count.
3. Clicks a function card → enters **sunburst view** for that function.
4. Reads per-function stat strip (job families, capabilities, role instances, distinct skills).
5. Views side list panel of job families; clicks a wedge or list item to zoom into a job family.
6. Reads capability names as sub-wedges.
7. Navigates **back** via float button or breadcrumb trail ("All functions / [Function name]").

### End-to-End Journey: HR / Talent BP (Skill Deep-Dive)

1. **Landing page** → selects a function.
2. **Sunburst view** → zooms into a job family wedge.
3. Clicks a capability (leaf node) → enters **detail view**.
4. Reads: capability tag, title, definition, role chips (with grades).
5. Reviews **skill clusters** — collapsible sections showing each skill with:
   - Type badge (Functional / Technical)
   - Category badge (Core / Sunrise / Sunset)
   - Build / Buy / Borrow / Bot badges
   - Proficiency ladder (Basic → Intermediate → Advanced definitions)
   - "Show role proficiency" toggle → inline per-role table showing each role's required level
6. Scrolls to the **skill × role matrix** at the bottom for a cross-skill view.
7. Navigates back to sunburst via "Back" button.

**Alternatively** (via Search):
1. Types a skill name (≥2 chars) in the global search box.
2. Selects from up to 10 autocomplete results (showing skill + function + job family + capability path).
3. `jumpTo()` navigates: `enterFunction` → zoom to job family (after 30 ms) → `openDetail` for capability (after 750 ms) — animated sequence.

### Key Decision Points and Branches

- **Sunburst leaf vs. non-leaf click**: a leaf (capability) opens the detail view; a non-leaf (job family) triggers a zoom-in.
- **Back float button visibility**: shown only when zoomed into a node that has a parent (i.e. not at function root).
- **`EXCLUDED_NOTE` banner**: shown or hidden depending on whether a note string is populated — flags missing data quality.

### Error States and Edge Cases in UI

- Skills with no proficiency level for a role render as empty/null cells in the matrix (no visual indicator defined in the extracted logic).
- Search returning zero results shows "No skills found."
- The `enterFunction` / `detailBack` interaction has a noted code risk: if `focus` is not set before `detailBack` fires, the back navigation could break (see §6).

---

## 3. App Flow Structure

### Screen / View Inventory

| View | ID / Element | Description |
|---|---|---|
| **Landing** | `#funcGrid` + stat strip | Grid of function cards; org-wide stats |
| **Sunburst** | `#stage` + `#sunburst` SVG | D3 zoomable sunburst for one function |
| **Detail** | `#detail` | Capability deep-dive: clusters, skills, matrix |
| **Search overlay** | `.searchwrap` / results dropdown | Floating search panel over any view |
| **Back float button** | `#backfloat` | Contextual back button over sunburst |

### Navigation Map

```
Landing (funcGrid)
  │
  ├─[Click function card]────────────────► Sunburst View (function root)
  │                                              │
  │                                              ├─[Click job-family wedge]──► Sunburst (zoomed to job family)
  │                                              │     │
  │                                              │     ├─[Click capability leaf]──► Detail View
  │                                              │     │                                 │
  │                                              │     │                           [Back button]
  │                                              │     │                                 │
  │                                              │     └─────────────────────────────────┘
  │                                              │
  │                                              ├─[Back float / breadcrumb]──► Landing
  │                                              │
  │                                              └─[Brand logo click]──────────► Landing
  │
  └─[Search result click]─────────────────────────────────────────────────────► Detail View
                                                                                  (via jumpTo animation)
```

### Multi-step Workflows

1. **Zoom workflow**: Landing → Function → Job Family → Capability (3 clicks minimum).
2. **Search shortcut**: any view → type ≥2 chars → select result → animated jump to detail (3-phase: 0ms, 30ms, 750ms transitions).
3. **Breadcrumb navigation**: "All functions" link → `goLanding()`; function name link → `enterFunction` without animation.

### Entry and Exit Points

- **Entry:** Landing page rendered on load via IIFE `renderLanding()`.
- **Exit:** No explicit exit; browser tab close or navigation away.

---

## 4. Data Structure & Data Flow

### Entity Model

#### `Function` (top-level)
| Field | Type | Notes |
|---|---|---|
| `name` | string | e.g. "Commercial Operations", "Finance", "HR", "COO", "CEO Office", "Creative & Design", "Property" |
| `job_families` | `JobFamily[]` | |

#### `JobFamily`
| Field | Type | Notes |
|---|---|---|
| `name` | string | e.g. "Merchandising", "Procurement", "Cash & Banking", "Graphic Design" |
| `sub_job_families` | `SubJobFamily[]` | May be empty; some job families have roles/clusters directly |

#### `SubJobFamily`
| Field | Type | Notes |
|---|---|---|
| `name` | string | e.g. "Space Planning and Design", "Data Management and Processing" |
| `roles` | `Role[]` | |
| `clusters` | `Cluster[]` | |

#### `Role`
| Field | Type | Notes |
|---|---|---|
| `role` | string | e.g. "Lead-CCM Supplier Performance", "Senior Manager-Audit & Recovery" |
| `grade` | string | e.g. "WL 1 (Lead)", "WL 2 (Senior Manager)", "WL 4 (Director)" |

#### `Cluster`
| Field | Type | Notes |
|---|---|---|
| `name` | string | e.g. "Sourcing & Procurement", "AI & Automation", "Data & Analytics" |
| `skills` | `Skill[]` | |

#### `Skill`
| Field | Type | Notes |
|---|---|---|
| `skill` | string | Skill name |
| `type` | `"Functional" \| "Technical"` | |
| `category` | `"Core" \| "Sunrise" \| "Sunset"` | |
| `definition` | string | General definition |
| `basic_def` | string | What Basic proficiency looks like |
| `intermediate_def` | string | What Intermediate proficiency looks like |
| `advanced_def` | string | What Advanced proficiency looks like |
| `build` | boolean | Build internally |
| `buy` | boolean | Hire/buy in |
| `borrow` | boolean | Contract/borrow |
| `bot` | boolean | Automate |
| `role_levels` | `("Basic" \| "Intermediate" \| "Advanced" \| null)[]` | One entry per role in parent sub-job family, in order |

### Full Function Inventory (extracted from data)

| # | Function Name |
|---|---|
| 1 | Commercial Operations |
| 2 | Digital/Internet Marketing |
| 3 | Email Marketing |
| 4 | Search Engine Marketing |
| 5 | Merchandising (incl. Merchandising Operations, Space Planning) |
| 6 | Procurement (incl. Procurement Intelligence, Procurement Operations) |
| 7 | Legal, Risk & Compliance Advisory |
| 8 | CEO Office (incl. Transaction Processing, General Management, Business Strategy) |
| 9 | Sales & Marketing |
| 10 | Supply Chain & Logistics |
| 11 | General Management |
| 12 | Creative & Design (Graphic Design) |
| 13 | COO (incl. Business Strategy Planning & Transformation, Solution Design, Applied Intelligence) |
| 14 | Finance (R2R, Cash & Banking, Financial Accounting, Corporate Finance, Cash Management, Contract Management, Audit, Taxation) |
| 15 | HR (HR Systems, Pensions, Learning & Development, General Management, Project Management) |
| 16 | Property (Real Estate & Asset Management, Property Operations, Lease & Asset Management, Acquisitions & Valuation) |

### Skill Cluster Taxonomy (representative, cross-function)

- Sourcing & Procurement
- Space Planning, Retail Space Planning
- Merchandising & Planning
- Data & Analytics
- AI & Automation *(universal Sunrise cluster)*
- Procurement Technology
- Legal Advisory
- Risk & Compliance
- Data Privacy
- Cost & Commercial Management
- Corporate Finance, Strategy & Investment
- Accounting, Close & Reporting
- Systems, Automation & Digital Enablement
- FP&A, Performance & Business Partnering
- Operations & Service Delivery
- Quantity Surveying & Cost Control
- Acquisitions & Disposals
- Valuation Methods & Analytics
- Corporate Affairs & ESG
- Organization Strategy
- Digital, Data & AI
- Design & Storytelling
- Corporate Communication
- Leadership & People Management
- Communication & Influence

### Universal AI / Sunrise Skills (appear in all functions)

| Skill | Type | Category |
|---|---|---|
| AI Literacy | Functional | Sunrise |
| Prompt Engineering | Technical | Sunrise |
| MS Copilot | Technical | Sunrise |
| Reading, Validating & QA-ing AI Outputs | Functional | Sunrise |
| Workflow Redesign with AI | Functional | Sunrise |
| Data & Process Readiness for AI | Functional | Sunrise |
| AI Adoption | Functional | Sunrise |
| Mistral AI | Technical | Sunrise |
| AI Use-Case Identification | Functional | Sunrise |
| AI Governance and Risk | Functional | Sunrise |
| Agentic AI & Multi-model Orchestration | Technical | Sunrise |

### Data Flow

| Data | Source | Notes |
|---|---|---|
| All playbook data | `capdata` JS variable (embedded JSON) | ~1.6MB of inline JSON; no API calls |
| Org-wide stats | Computed by `funcStats()` on load | Counts of functions, job families, capabilities, skills, role instances |
| Per-function stats | Computed by `renderFuncStats()` | Same counts scoped to selected function |
| Search index | `buildSearchIndex()` on load | Flat array of all skills with their navigation path |
| Color assignments | `PALETTE` array (9 hex colors) | Assigned by job-family index within a function |
| Proficiency shading | `shade(hex, t)` | Intermediate = 45% shade; Basic = 80% shade |

### Computed / Derived Fields

| Field | Derivation |
|---|---|
| Job family count per function | `fn.job_families.length` |
| Capability count | Sum of sub-job families across all job families |
| Role instance count | Sum of all roles across all sub-job families |
| Distinct skill count | Deduplicated union of all skill names in a function |
| Arc color per capability | `shade(jobFamilyColor, index / siblingCount)` |
| Conic-gradient ring on landing card | Capability count per job family as proportion of total |

### API Patterns

There are **no API calls** (`fetch()`, `XMLHttpRequest`, or otherwise). All data is embedded as a single `capdata` JavaScript object in the HTML file. The only external network requests are:
- Google Fonts CDN (`fonts.googleapis.com`, `fonts.gstatic.com`) for Manrope typeface
- `cdnjs.cloudflare.com` for D3 v7.9.0

---

## 5. Dashboard Components & UX

### Component Inventory

#### Landing View

| Component | Purpose | Data | Interactions |
|---|---|---|---|
| **Org-wide stat strip** | Top-level KPIs for GCC | Functions count, Job families count, Capabilities count, Distinct skills count, Role instances count | Display only |
| **`#noteBanner`** | Excluded-workbook warning | `EXCLUDED_NOTE` string | Display only; hidden if empty |
| **Function card grid (`#funcGrid`)** | Entry point per function | Function name, conic-gradient ring (job-family split by capability count) | Click → `enterFunction(i)` |
| **Conic-gradient ring** | Visual proportion of job families within function | Capability count per job family | Display only |
| **Brand bar ("Tesco Business Solutions · GCC Playbook")** | Global header / home link | Static | Click → `goLanding()` |

#### Sunburst View

| Component | Purpose | Data | Interactions |
|---|---|---|---|
| **D3 Sunburst SVG (`#sunburst`, 900×900)** | Hierarchical capability map for one function | Function → Job families → Capabilities (sub-job families) as arc wedges | Click wedge → zoom in or open detail |
| **Center hit circle** | Back / zoom-out target | Currently focused node name | Click → `zoomOutOrHome()` |
| **Center label (two tspans)** | Shows focused node name | `focus.data.name` | Display only |
| **Per-function stat strip** | KPIs scoped to current function | Job families, capabilities, role instances, distinct skills | Display only |
| **Side list panel** | Text navigation complement to sunburst | Job families or capabilities depending on zoom level | Click → zoom or open detail |
| **Breadcrumb (`#topBreadcrumb`)** | "All functions / [Function]" trail | Dynamically set | Click segments → `goLanding()` or `enterFunction()` |
| **Back float button (`#backfloat`)** | Contextual back navigation | Back button label (set by `updateCenterLabel`) | Click → `zoomOutOrHome()` |
| **Skill search box** | Global skill lookup | All skills flattened into `SEARCH_INDEX` | Type ≥2 chars → dropdown of ≤10 results |
| **Search results dropdown** | Autocomplete results | Skill name + function + job family + capability path | Click → `jumpTo()` |

#### Detail View

| Component | Purpose | Data | Interactions |
|---|---|---|---|
| **Capability tag + title** | Identity header | Sub-job family name and parent job family | Display only |
| **Capability definition** | Description of the capability | Sub-job family `definition` | Display only |
| **Role chips** | Shows roles and grades in this capability | Role name + grade badge | Display only |
| **Sourcing count strip** | Counts skills by sourcing type | Sum of build/buy/borrow/bot flags | Display only |
| **Level legend** | Color key for proficiency shading | Basic (80% shade) / Intermediate (45% shade) / Advanced (full color) | Display only |
| **Collapsible skill clusters** | Organises skills by cluster | Cluster name as toggle header | Click cluster-head → toggle `open` class |
| **Skill card (per skill)** | Full skill profile | Skill name, type badge, category badge, definition, sourcing badges (BBBB), proficiency ladder | "Show role proficiency" toggle → inline role table |
| **Proficiency ladder** | Three-row display of Basic / Intermediate / Advanced definitions | `basic_def`, `intermediate_def`, `advanced_def` | Display only |
| **Per-role proficiency table** | Which roles need this skill at which level | `role_levels` array mapped to roles | Shown/hidden by toggle |
| **Skill × Role matrix (`#detailMatrix`)** | Full cross-reference grid | All skills × all roles in sub-job family; cells color-coded by level | Display only (no sorting/filtering) |
| **Detail back button (`#detailBack`)** | Return to sunburst | Context-aware label | Click → hide detail, show stage |

### Color System

| Element | Color logic |
|---|---|
| Job family arcs | `PALETTE[index]` — 9 hardcoded hex colors |
| Capability arcs (sub-wedges) | `shade(jobFamilyColor, i / n)` — lighter shades for deeper siblings |
| Advanced proficiency cell | Full job-family color |
| Intermediate proficiency cell | 45% shade toward white |
| Basic proficiency cell | 80% shade toward white (very light) |
| Sunrise category badge | Distinct color defined in `BBBB`-style mapping (exact hex not extracted) |
| Build / Buy / Borrow / Bot badges | Each of the four has its own icon + color |

### Layout and Information Hierarchy

1. **Primary:** Sunburst chart (900×900, center stage) — dominant visual
2. **Secondary:** Side list panel — textual navigation complement
3. **Supporting:** Stat strip (above chart), breadcrumb (top), back float button (overlay)
4. **Deep-dive:** Detail view (replaces sunburst entirely) with matrix at the bottom as the most data-dense element

### UX Patterns Used

| Pattern | Usage |
|---|---|
| **Drill-down** | Landing → Function → Job Family → Capability (3 levels) |
| **Breadcrumb navigation** | "All functions / [Function]" trail with clickable segments |
| **Animated zoom** | 700 ms cubic in-out D3 transition on wedge click |
| **Collapsible sections** | Cluster headings in detail view toggle `open` class |
| **Progressive disclosure** | "Show role proficiency" toggle on each skill card |
| **Search with autocomplete** | Global skill search, ≥2 chars, ≤10 results |
| **Deep-link via `jumpTo`** | Three-phase animated navigation from search to detail |
| **Color as data encoding** | Proficiency level expressed through saturation shading |

### UX Issues and Gaps

| Issue | Detail |
|---|---|
| **Matrix is display-only** | The skill × role matrix has no sorting, filtering, or export. For 50+ skills and 20+ roles, this is hard to scan. |
| **No role-centric entry point** | Users cannot search or browse by role — only by skill or by function/capability. A "find all skills for role X" path doesn't exist. |
| **Null proficiency not visually distinguished** | Cells where a role does not require a skill render differently from "Basic" but no legend entry explains the null state. |
| **No mobile layout** | The sunburst is hardcoded at 900×900 SVG; no responsive sizing is evident. |
| **Search scope is skill names only** | Search matches on skill name only, not on definition text, cluster name, or capability name. |
| **`skipAnim` parameter unused** | `enterFunction(fnIdx, skipAnim)` accepts but never uses `skipAnim`, meaning the animation cannot be suppressed programmatically. |
| **`detailBack` / `focus` risk** | If `openDetail` is reached without `focus` being set (e.g. via a stale deep-link), `detailBack` click will error. No guard exists. |
| **Excluded workbook** | At least one function's data is missing from the playbook (no proficiency ratings). The banner notes this but doesn't name the function, leaving users to guess. |
| **Typo in data** | "Financial Accouning" (missing 't') appears twice as a job family name. |
| **No print or export** | No PDF export, CSV download, or shareable URL. |
| **Back button label ambiguity** | `updateCenterLabel()` sets the back button text to the parent node's name — but at the function root, the button navigates to "Landing," which is not labeled "All functions" on the button itself. |

---

## 6. Open Questions & Assumptions

### Ambiguities and Incomplete Signals

| # | Issue |
|---|---|
| 1 | **Which function is excluded?** The `EXCLUDED_NOTE` banner fires when a string is set, but the excluded function's identity is not visible in the extracted code. The product owner must clarify which business unit is missing and the timeline for including it. |
| 2 | **Are "CrossWalkers" roles permanent headcount or a rotation programme?** The role naming ("CrossWalkers associate (C&P)", "CrossWalkers associate (C&P-PS)") implies a structured mobility programme, but no metadata about rotation duration or eligibility exists in the data. |
| 3 | **Is the `capdata` JSON meant to be replaced by an API?** Currently all data is embedded inline (~1.6MB). The prototype is clearly not production architecture — the path to a CMS or API-backed data layer is unspecified. |
| 4 | **Who maintains the playbook?** There is no authoring interface, version field, or last-updated timestamp in the data or UI. |
| 5 | **Are Build/Buy/Borrow/Bot flags editable by function leads?** The UI treats them as read-only, but in a workflow context they likely represent decisions that change over time. |
| 6 | **What triggers a skill's category changing from Sunrise to Core?** No lifecycle management logic or governance process is defined. |
| 7 | **Is the 9-color palette sufficient?** `PALETTE` has 9 colors. If a function has more than 9 job families, colors will repeat. The maximum job family count per function is not validated. |
| 8 | **What is the intended output for users?** The playbook is read-only. No downstream action (e.g. training plan creation, gap analysis export, headcount request) is wired in. |
| 9 | **Should search match on definitions and cluster names, not just skill names?** Current search is limited to skill name matching — potentially frustrating if a user searches for "contract" and expects to find skills in "Commercial & Contract Management" clusters. |
| 10 | **What does "General" cluster mean?** Several sub-job families have a cluster simply labeled "General" with no further taxonomy. This may be a placeholder. |

### Assumptions Made

- The top-level data object is named `capdata` (inferred from code references; not directly verified in the opening HTML).
- The "Excluded note" is a single string variable (`EXCLUDED_NOTE`), not a list — meaning only one workbook is currently excluded.
- The prototype is a single HTML file with no build system, no external CSS/JS files (other than CDN libraries), and no backend.
- The "GCC" in TBS GCC refers to Tesco's Global Capability Centre operating model (offshore/nearshore centre structure), consistent with the Tesco Business Solutions brand.
- The grade structure (WL 1–4) is Tesco's internal Work Level framework.
- `PALETTE` assigns colors per job family by positional index within the currently rendered function, not globally — so the same job family name in two functions may have different colors.

### Items Requiring Product Owner Clarification Before Development

1. Confirm the production data strategy: embedded JSON vs. CMS vs. REST API.
2. Define the authoring/editing workflow for skills data updates.
3. Specify role-based access control requirements (read-only for all users? Edit access for HR admins?).
4. Confirm whether a download/export capability (PDF, Excel) is required.
5. Clarify responsive/mobile requirements given the 900×900 SVG chart.
6. Define the excluded workbook and the timeline for its inclusion.
7. Specify whether the `jumpTo` deep-linking pattern needs to support a shareable URL (currently no URL routing exists).
8. Confirm the maximum number of job families per function to validate the 9-color `PALETTE` is sufficient.
