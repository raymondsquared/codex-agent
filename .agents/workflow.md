# Workflow.md

## Workflow Overview

Discovery -> Design -> Plan -> Build

```
Orchestrator ->
1. Discovery: Market Research ->
2. Discovery: Press Release (PR) and Frequently Asked Questions (FAQ) ->
3. Design: Product Requirements Document (PRD) ->
4.1. Design: User Interface / User eXperience Design System ->
4.2. Design: Create Architecture Design Document ->
4.3. Design: Secure by Design ->
5. Plan ->
6. Build
```

| Phase | Actor/Output        | Guide to Load                              |
| ----- | ------------------- | ------------------------------------------ |
| ALL   | Orchestrator        | `.agents/specialists/orchestrator.md`      |
| ALL   | Reviewer            | `.agents/specialists/devils-advocate.md`   |
| 1     | Market Research     | `.agents/specialists/market-researcher.md` |
| 2     | PR FAQ              | `.agents/specialists/product-manager.md`   |
| 3     | PRD                 | `.agents/specialists/product-manager.md`   |
|       |                     | `.agents/specialists/business-analyst.md`  |
| 4.1   | UI/UX Design System | `.agents/specialists/ui-ux.md`             |
| 4.2   | System Architecture | `.agents/specialists/architect.md`         |
| 4.3   | Secure By Design    | `.agents/specialists/security.md`          |
| 5     | Plan                | `.agents/specialists/project-manager.md`   |
| 6.1   | Data                | `.agents/specialists/data-engineer.md`     |
| 6.2   | Software            | `.agents/specialists/software-engineer.md` |
| 6.3   | CI/CD               | `.agents/specialists/devops-engineer.md`   |
| 6.4   | QA                  | `.agents/specialists/quality-assurance.md` |

All outputs saved to `docs/`. See phase-specific Save sections for exact formats.
All inter-agent data transfers follow the schema in `.agents/prompts/handoff.md`.

### Handoff Persistence

After completing each phase, the Orchestrator MUST save the handoff payload to:

```
docs/handoffs/handoff-{{SESSIONID}}-{{SOURCE}}-{{TARGET}}-{{YYYYMMDD}}.json
```

After saving, the Orchestrator MUST validate the handoff by invoking the
`validate-handoff` skill (`.agents/skills/validate-handoff/SKILL.md`).
A phase is not complete until all REQUIRED checks pass.
If validation fails, fix the payload and re-validate.

At the start of each phase, the receiving agent loads the most recent handoff for the current product and session. This ensures runs are reproducible, re runnable, and auditable.

During Discovery:

- Product Manager: Conduct Market Research on the product.
- Reviewer: Review Market Research.
- Product Manager: Prepare prfaq.md for the product.
- Reviewer: Review PR and FAQ.

During Design:

- Business Analyst: Create PRD requirements.md for the product.
- Reviewer: Review PRD.
- UI/UX: Create UI/UX Design System for the product
  (if needed for UI/UX work).
- Reviewer: Review UI/UX.
- Architect: Create a Design Document
  (architecture, technology choices, and integration) for the product (optional but most of the time needed for complex or ambiguity work).
- Reviewer: Review Design Document.
- Security: Create Secure by Design review for the product
  (threat modelling, security controls, and compliance).
- Reviewer: Review Security Document.

During Plan:

- Project Manager: Create Project Plan.
- Reviewer: Review Project Plan.

During Build:

- Software Engineer: Execute software feature tasks.
- Software Engineer: Verify software feature tasks.
- Reviewer: Review feature software feature tasks.
- DevOps Engineer: Execute devops feature tasks.
- DevOps Engineer: Verify devops feature tasks.
- Reviewer: Review feature devops feature tasks.
- QA: QA feature tasks.
- QA: Test the feature.
- QA: Test the whole product.

## Step 1: Orchestrator

### Initialise Session

Before asking the user anything, generate a UUID and save it to `.agents/.session`.
This session ID is used in all handoff filenames (`{{SESSIONID}}`) and handoff payloads (`sessionId`).
If `.agents/.session` already exists, read and reuse the existing value (this is a resumed session).

Session state is persisted to `docs/handoffs/session-state-{{SESSIONID}}.json`.
If this file exists, load it and resume from `currentPhase` and `phasesCompleted` without re-asking intake questions.
Update this file after every phase by appending the completed phase to `phasesCompleted`, advancing `currentPhase`, and setting `updatedAt`.
SEE `.agents/specialists/orchestrator.md` Step 3 for the full session state schema and recovery procedure.

### Context Window Management

This workflow spans many phases and can easily exceed an LLM context window. To prevent
corruption or silent context loss, these rules apply at all times:

- Each specialist agent runs with a minimal context: its persona file, the inbound handoff
  JSON, and the standards files it references. Full conversation history is never passed.
- After a phase completes and its handoff is saved, the Orchestrator discards raw phase
  output from active context. Only the saved handoff path and artefact paths are retained.
- The session state file (`docs/handoffs/session-state-{{SESSIONID}}.json`) and the most
  recent handoff file are the only persistent inputs the Orchestrator needs to resume.
- If the Orchestrator context appears saturated (responses degrade or prior phase details are
  misremembered), start a new conversation, load `.agents/.session` to recover the session ID,
  load `docs/handoffs/session-state-{{SESSIONID}}.json` to determine position, then load the
  most recent handoff JSON and continue from `currentPhase`.

### Ask the user:

- What problem are you trying to solve?
- Who is your target audience/customer?
- What are your main business goals?
- What key features do you envision?
- How will users interact with this product?
- Is this for an existing company? (for brand research)

### Inform user about workflow mode:

After gathering initial information, tell the user:

> "I'll work through 6 phases: 1. Market Research -> 2. PR FAQ -> 3. PRD -> 4.1. UI/UX Design System and prototypes (optional) -> 4.2. Architecture Design Document -> 4.3. Secure By Design Document-> 5. Plan -> 6. Build. By default, I'll pause after each phase for your feedback. If you'd prefer I work through everything continuously, just say 'switch to Automatic' at any time."

### Workflow Modes

### Manual Mode (DEFAULT)

- After completing each phase, STOP and present a summary
- Ask: "Ready to proceed to the next phase, or would you like changes?"
- Do NOT proceed until the user explicitly approves
- This allows for course corrections and ensures quality

### Automatic Mode

- Progress through all phases automatically without pausing
- Only stop to ask questions if critical information is missing
- User can interrupt at any time to review or change direction

Users can switch modes at any time by saying:

- "switch to automatic mode": to work through phases continuously
- "switch to manual mode": to pause and review after each phase

### Reviewer Role

Review findings are included in phase summaries for the user's consideration.
The user always makes the final decision on whether to act on them.

- MEDIUM or LOW findings: Advisory only and never block progress.
  They are recorded in the phase summary for reference.
- CRITICAL or HIGH findings: The Orchestrator MUST pause and present the
  findings to the user, regardless of workflow mode, and follow the
  fix -> re-review loop:
  1. Present findings and ask the user to Fix or Override.
  2. If Fix: apply changes and re-run the reviewer (max 2 re-review iterations).
  3. If Override: record the rationale in the phase summary and proceed.
  4. After 2 failed re-reviews with result still REJECTED, escalate:
     the user must explicitly Override (accept risk) or Abandon the phase.
     Their decision and rationale are recorded before proceeding.

SEE `.agents/specialists/orchestrator.md` Phase Execution Protocol Step 4 for the full loop definition.

## Step 2: Spec Driven Development

### Phase 1: Discovery - Market Research

Load:

- `.agents/specialists/market-researcher.md`

> Important: Use web search to find real competitor data, pricing, market reports, and customer reviews. Don't make up data.

Conduct web based research:

- Competitive Landscape (3-5 competitors)
  - Product offerings and positioning
  - Pricing models with actual figures
  - Strengths and weaknesses
- Market Sizing
  - TAM (Total Addressable Market) with sources
  - SAM (Serviceable Addressable Market)
  - SOM (Serviceable Obtainable Market)
- Customer Pain Points
  - From reviews, forums, social media
  - Specific quotes and examples
  - Unmet needs in current solutions
- Pricing Intelligence
  - Competitor pricing tiers
  - Industry benchmarks
- Brand Research (if building for existing company)
  - Logo, colours, typography
  - Brand voice and positioning

Save:

- `docs/discovery/marketresearch-{{PRODUCT}}-{{YYYYMMDD}}.html`
- `docs/discovery/marketresearch-{{PRODUCT}}-{{YYYYMMDD}}.json`

Checkpoint:

- [ ] TAM/SAM/SOM with dollar figures and sources
- [ ] At least 3 competitors with real pricing
- [ ] Pain points are specific, not generic
- [ ] No placeholder text (TBD, TODO, [insert])
- [ ] File saved successfully

> Manual Mode: STOP here. Present summary and wait for user approval before proceeding.

### Phase 2: Discovery - Press Release (PR) and Frequently Asked Questions (FAQ)

Load:

- `.agents/specialists/product-manager.md`

Incorporate Market Research findings, then create:

- Work through 5 Working Backwards questions:
  - Who is the customer? (use research insights)
  - What is the customer problem? (use pain points from research)
  - What is the solution?
  - What is the customer experience?
  - How will we measure success?
- Write Press Release (as if product launched)
- Write FAQ (address skeptical questions)

Save:

- `docs/discovery/prfaq-{{PRODUCT}}-{{YYYYMMDD}}.html`
- `docs/discovery/prfaq-{{PRODUCT}}-{{YYYYMMDD}}.md`

Checkpoint:

- [ ] Incorporates market research findings
- [ ] Compelling headline (not generic)
- [ ] Customer problem is specific with data
- [ ] Solution addresses researched pain points
- [ ] FAQ addresses real concerns (not softballs)
- [ ] File saved successfully

> Manual Mode: STOP here. Present summary and wait for user approval before proceeding.

### Phase 3: Design - Product Requirements Document (PRD)

Load:

- `.agents/specialists/product-manager.md`
- `.agents/specialists/business-analyst.md`

Create from PRFAQ and Market Research:

- User personas with detailed profiles
- Requirements in EARS syntax (Check `.agents/principles.md`)
- User stories with acceptance criteria
- Success metrics and business model
- Technical constraints
- Minimum Lovable Product (MLP) Testing Plan (mandatory)

Save:

- `docs/design/prd-{{PRODUCT}}-{{YYYYMMDD}}.html`
- `docs/design/prd-{{PRODUCT}}-{{YYYYMMDD}}.md`

Checkpoint:

- [ ] Personas based on market research customer insights
- [ ] Requirements traceable to PRFAQ
- [ ] Competitive positioning informed by research
- [ ] Testing plan included
- [ ] Tech stack uses cloud native services
- [ ] Files saved successfully

> Manual Mode: STOP here. Present summary and wait for user approval before proceeding.

### Phase 4.1: Design - User Interface / User eXperience Design System

> Skip condition: If the product has no graphical user interface (e.g. API-only service, CLI tool, data pipeline, infrastructure module), skip Phase 4.1 and proceed to Phase 4.2.

Load:

- `.agents/specialists/ui-ux.md`

Create from PRD (modular structure required):

1. Design System first: shared CSS tokens and components

- User flow mapping
- Information architecture
- Individual screen HTML files (NOT one monolithic file)
- Clickable build with navigation
- Form validation and interactions
- Project Dashboard (navigation hub)

> Critical: Connect All Screens Together
>
> Every button, link, and navigation element must work:
>
> - Use `href="screen-{{NAME}}-{{PRODUCT}}-{{YYYYMMDD}}.html"` for links between screens
> - Navigation menus should link to all main screens
> - "Back" buttons should return to the previous screen
> - Form submissions should navigate to success/confirmation screens
> - Dashboard cards should link to their detail screens
> - User flows must be completable end to end by clicking through
>
> Test every link before marking the build complete.

Save:

- `docs/design/designsystem-{{PRODUCT}}-{{YYYYMMDD}}.html` (create FIRST)
- `docs/design/screenindex-{{PRODUCT}}-{{YYYYMMDD}}.html` (navigation hub)
- `docs/design/screen-{{NAME}}-{{PRODUCT}}-{{YYYYMMDD}}.html` (one per screen)
- `docs/design/clickablebuild-{{PRODUCT}}-{{YYYYMMDD}}.html`
- `docs/design/projectdashboard-{{PRODUCT}}-{{YYYYMMDD}}.html`

Checkpoint:

- [ ] Design system created FIRST with shared CSS
- [ ] Modular structure (separate files per screen)
- [ ] All PRD screens implemented
- [ ] All buttons and links navigate to correct screens
- [ ] User flows completable end to end
- [ ] Forms have validation
- [ ] Responsive on mobile/tablet/desktop
- [ ] Realistic data (no Lorem ipsum)
- [ ] Follows standards (no AI slop)
- [ ] Files saved successfully
- [ ] Ready for 4.2: screen list and user flows are documented so the architect can reference them

> Manual Mode: STOP here. Present summary and wait for user approval before proceeding.

### Phase 4.2: Design - Create Architecture Design Document

Load:

- `.agents/specialists/architect.md`

Create from PRD:

- Design Document file based on `.agents/templates/designdocument.md`

Save:

- `docs/design/designdocument-{{PRODUCT}}-{{YYYYMMDD}}.md`

Checkpoint:

- [ ] Design Document file based on `.agents/templates/designdocument.md`
- [ ] Rendering model (SSR / SPA / native) is consistent with the UI/UX prototype from 4.1 — if not, note the discrepancy and update the architecture doc or screen index to align before proceeding
- [ ] Technology choices do not contradict PRD technical constraints
- [ ] Files saved successfully

> Manual Mode: STOP here. Present summary and wait for user approval before proceeding.

### Phase 4.3: Design - Secure by Design

Load:

- `.agents/specialists/security.md`

Create from PRD:

- [ ] Secure by Design file based on `.agents/templates/securebydesign.template.md`
- Technical constraints

Save:

- `docs/design/securebydesign-{{PRODUCT}}-{{YYYYMMDD}}.md`

Checkpoint:

- [ ] Security Design document created
- [ ] All required security screens (MFA, consent, session timeout) are present in the 4.1 screen list — if missing, add them to the screen index before proceeding
- [ ] Data classification is consistent with data stores chosen in 4.2 — if not, update the affected document before proceeding
- [ ] Files saved successfully

> Manual Mode: STOP here. Present summary and wait for user approval before proceeding.

### Phase 5: Plan

Load: `.agents/specialists/project-manager.md`

Create from PRD:

1. Features specification in EARS requirements

Save:

- `docs/plan/feature-{{FOURDIGITSNUMBER}}-{{FEATURENAME}}/requirements.md`

Checkpoint:

- [ ] Each requirement will have its own feature folder
- [ ] Requirement file based on `.agents/templates/requirements.md`
- [ ] Requirement traceable to PRD
- [ ] Requirement should be more thorough with acceptance criteria than the main prd.md
<!-- - [ ] Tasks should be a break down of the feature's requirements.md -->
- [ ] Files saved successfully
- [ ] Every feature folder must have `requirements.md` (SEE `.agents/templates/`),
      and both files should reflect the same feature ID so downstream work remains traceable.
      Keep acceptance criteria EARS compliant.

> Manual Mode: STOP here. Present summary and wait for user approval before proceeding.

### Phase 6: Build - Software Development LifeCycle

Build proceeds in two passes to prevent cross-feature data model conflicts:

Pass 1 — Data layer (all features)
Run Phase 6.1 for every feature before starting Phase 6.2 on any feature.
After all 6.1 runs complete, the Data Engineer reconciles schemas across features:

- Merge duplicate or overlapping tables/entities into shared definitions
- Align shared concerns (auth, users, audit logs) into a single canonical schema
- Version unified DTOs/contracts before any feature proceeds to 6.2

All code saves to `src/` (language/framework appropriate structure).

Pass 2 — Software, CI/CD, QA (per feature)
With the reconciled data layer in place, execute sub-phases 6.2 through 6.4 per feature.
Complete one feature end to end (6.2 → 6.3 → 6.4) before starting the next.

All sub-phases take the feature `requirements.md` from Phase 5 as input.

#### Phase 6.1: Data (run for ALL features first)

Load: `.agents/specialists/data-engineer.md`

Create: database schemas/migrations, data models/entities, DTOs/API contracts, data pipelines (if applicable).

Checkpoint (per feature):

- [ ] Follows `.agents/standards/data.md` and language-appropriate naming
- [ ] DTOs separate request and response; contracts versioned
- [ ] No hardcoded credentials or secrets

Checkpoint (after all features complete 6.1 — before any feature moves to 6.2):

- [ ] Shared entities (e.g. User, Auth, AuditLog) exist once and are referenced by all features that need them
- [ ] No duplicate tables or conflicting column definitions across feature schemas
- [ ] Unified DTOs and contract version published to `src/` before 6.2 begins

#### Phase 6.2: Software

Load: `.agents/specialists/software-engineer.md`

Create: application code, unit tests covering acceptance criteria, integration with data layer from 6.1.

Checkpoint:

- [ ] Follows `.agents/principles.md` and `.agents/standards/languages/{{SPECIFIC_LANGUAGES}}.md`
- [ ] Unit tests cover each acceptance criterion; `make test` passes
- [ ] No hardcoded credentials or secrets

#### Phase 6.3: CI/CD

Load: `.agents/specialists/devops-engineer.md`

Create: build pipeline (CI), deployment automation (CD), Infrastructure as Code (Terraform if applicable).

Checkpoint:

- [ ] `make build` passes; pipeline runs lint, test, and build stages
- [ ] Secrets via environment variables or secrets manager; no manual steps
- [ ] Infrastructure defined as code

#### Phase 6.4: QA

Load: `.agents/specialists/quality-assurance.md`

Create: integration tests (end-to-end), regression tests, acceptance test report against `requirements.md`.

Checkpoint:

- [ ] Every acceptance criterion has a corresponding test
- [ ] `make test` passes (unit + integration); no regressions
- [ ] Edge cases and error paths covered

> Manual Mode: STOP here after each feature. Present summary and wait for user approval before proceeding to the next feature.

### Phase 6 Failure Recovery Policy

If a sub-phase checkpoint fails (e.g. `make test` fails in 6.2, pipeline fails in 6.3):

1. Log which checkpoint item(s) failed and the reason.
2. Retry once — re-run the failed sub-phase with the same inputs.
3. If the retry also fails, STOP and present the failure to the user with these options:
   - Fix: User provides additional context or guidance; re-run the sub-phase.
   - Skip: Skip this sub-phase for the current feature and note it as incomplete (acceptable for non-blocking sub-phases like 6.3 when infrastructure is pre-existing).
   - Abandon feature: Mark the feature as blocked; proceed to the next feature and return to this one later.
4. Never silently skip a failed sub-phase. Every failure must be recorded in the phase summary and the session state file.

---

## File Naming Convention

Discovery, Design and Plan Phases:

```
docs/{{PHASE}}/{{TYPE}}-{{PRODUCT}}-{{YYYYMMDD}}.{{EXTENSION}}
```

Examples:

- `docs/discovery/marketresearch-smartinventory-20250106.html`
- `docs/discovery/prfaq-smartinventory-20250106.html`
- `docs/design/prd-smartinventory-20250106.html`
- `docs/design/screen-dashboard-smartinventory-20250106.html`

Build Phase:

```
src/
```

Rules:

- Remove spaces
- Use lower case for product name
- Same date revisions overwrite the original file (Git provides version history)
- Different date revisions are preserved as separate files.

## File Structure After Completion

```
│
├── docs/
|     |
|     ├── handoffs/
│     |   └── handoff-{{SESSIONID}}-{{SOURCE}}-{{TARGET}}-{{YYYYMMDD}}.json
|     |
│     ├── discovery/
│     │   ├── marketresearch-{{PRODUCT}}-{{YYYYMMDD}}.json      # Phase 1
│     │   ├── marketresearch-{{PRODUCT}}-{{YYYYMMDD}}.html      # Phase 1
│     │   ├── prfaq-{{PRODUCT}}-{{YYYYMMDD}}.md                 # Phase 2
│     │   └── prfaq-{{PRODUCT}}-{{YYYYMMDD}}.html               # Phase 2
│     │
│     ├── design/
│     │   ├── prd-{{PRODUCT}}-{{YYYYMMDD}}.html                 # Phase 3
│     │   ├── prd-{{PRODUCT}}-{{YYYYMMDD}}.md                   # Phase 3
│     │   ├── {{PRODUCT}}.css                                   # Phase 4.1  Shared CSS (create first)
│     │   ├── designsystem-{{PRODUCT}}-{{YYYYMMDD}}.html        # Phase 4.1
│     │   ├── projectdashboard-{{PRODUCT}}-{{YYYYMMDD}}.html    # Phase 4.1
│     │   ├── clickablebuild-{{PRODUCT}}-{{YYYYMMDD}}.html      # Phase 4.1
│     │   ├── screenindex-{{PRODUCT}}-{{YYYYMMDD}}.html         # Phase 4.1
│     │   ├── screen-{{NAME}}-{{PRODUCT}}-{{YYYYMMDD}}.html     # Phase 4.1
│     │   ├── designdocument-{{PRODUCT}}-{{YYYYMMDD}}.md        # Phase 4.2
│     │   └── securebydesign-{{PRODUCT}}-{{YYYYMMDD}}.md        # Phase 4.3
│     │
│     └── plan/
│           └── feature-{{FOURDIGITSNUMBER}}-{{FEATURENAME}}/
│                 └── requirements.md                           # Phase 5
│
└── src/                                                        # Phase 6
```

Quality Checklist

### After Each Phase:

- [ ] MD/JSON/HTML file saved with correct naming
- [ ] Content builds on previous phase
- [ ] Uses consistent design system styling
- [ ] No placeholder text (TBD, TODO, Lorem ipsum)
- [ ] Professional formatting and typography

### Cross Phase Consistency:

- [ ] Market research informs PRFAQ customer problem
- [ ] Personas consistent across PRFAQ -> PRD -> Design -> Plan -> Build
- [ ] Competitive positioning consistent throughout
- [ ] Success metrics coherent across documents
- [ ] Technical constraints carried forward

### Build Specific:

- [ ] Modular structure (NOT single monolithic file)
- [ ] Design system created FIRST
- [ ] Every PRD screen included
- [ ] All workflows completable end to end
- [ ] Mobile responsive on all screens
- [ ] Form validation works
- [ ] Navigation between all screens works
- [ ] Data consistent across screens

## Design Standards

### Anti Patterns (NEVER USE):

- Generic fonts: Inter, Roboto, Arial, system ui
- Purple to blue gradients on white
- Uniform card grids
- Bootstrap/Tailwind defaults without customisation
- Excessive emojis

### Required:

- Distinctive typography matching product aesthetic
- 60:30:10 colour rule (dominant/secondary/accent)
- Visual texture (gradients, shadows, depth)
- Bouncy animations for key moments (custom cubic-bezier)
- Modular file structure for prototypes

### Aesthetic Directions:

| Product Type      | Direction        | Key Traits                                 |
| ----------------- | ---------------- | ------------------------------------------ |
| Enterprise B2B    | Luxury/Refined   | Serif fonts, gold accents, subtle shadows  |
| Developer Tools   | Retro Futuristic | Dark mode, neon glows, monospace           |
| Consumer Apps     | Playful          | Rounded corners, bouncy animations, bright |
| Content Platforms | Editorial        | Strong typography, dramatic whitespace     |
| Dashboards        | Industrial       | Dense data, functional, efficient          |
| Wellness/Health   | Organic          | Earth tones, soft curves, calm             |

## Context Integration

When the user provides additional context (CSV files, company docs, team info):

Use it to:

- Inform market research with real company data
- Create realistic personas from real team members
- Use actual customer names and metrics
- Incorporate specific industry terminology
- Reflect actual business scale
- Make scenarios match real workflows

Quality standard: All examples should feel authentic to the provided context.

## Switching Modes & Handling Interruptions

User can switch modes anytime:

- "Switch to Automatic" -> Continue through remaining phases without pausing
- "Switch to Manual" -> Pause after each phase for feedback

If the user interrupts during automatic mode:

1. Pause immediately
2. Show current progress
3. Ask: "What would you like to review or change?"
4. Offer options:
   - Review current phase
   - Make changes
   - Switch to Manual mode
   - Continue after addressing feedback
5. Resume based on user preference

## Validation Requirements by Phase

### Market Research Validation:

- TAM/SAM/SOM with actual dollar figures
- Sources cited for market data
- At least 3 competitors with real pricing
- Pain points must be specific quotes/examples

### PRFAQ Validation:

- Headline is compelling and specific
- Customer problem grounded in research
- Solution is concrete, not vague
- FAQ addresses skeptical questions

### PRD Validation:

- Requirements in EARS syntax
- Acceptance criteria for each story
- Testing plan included
- Tech stack compliance

### Plan Validation:

- Verify if all features are available in the `docs/plan/feature-{{FOURDIGITSNUMBER}}-{{FEATURENAME}}`
- For each feature, make sure requirements.md file existed
<!-- - For each requirements.md, technical tasks are listed properly in tasks.md -->
- If needed, design document needs to be there
- If needed, check if dtos folder existed for contract or data transfer object
- If needed, check if entities folder existed for domain models or entities

### Build Validation:

- Modular file structure (not monolithic)
- Design system created first
- All screens from PRD implemented
- Navigation works between all screens
- No AI slop aesthetics

## Sample Outputs

SEE `.agents/samples/` folder for example outputs:

- `.agents/samples/prfaq-productname.sample.html`
- `.agents/samples/prd-productname.sample.html`
- `.agents/samples/designsystem-productname.sample.html`
- `.agents/samples/screen-dashboard-productname.sample.html`

Open in browser to see quality standards.

## Troubleshooting

### Files not saving?

- Ensure `docs/` folder exists (create if needed)
- Use correct naming convention
- Verify file creation before proceeding

### Market research feels thin?

- Use web search to find real competitor data
- Look for actual pricing pages
- Search for customer reviews and complaints
- Find industry reports for market sizing

### Content feels generic?

- Review all provided context thoroughly
- Use actual names, metrics, scenarios from data
- Create personas based on real people mentioned
- Match business scale to provided information

### Build looks like AI slop?

- Check against design standards anti patterns
- Use distinctive fonts (not Inter/Roboto)
- Avoid purple blue gradients
- Add visual texture and depth
- Reference `.agents/samples/designsystem-{{PRODUCT}}.sample.html`
