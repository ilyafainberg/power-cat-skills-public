---
name: "powercat-check-design"
description: "Evaluates a Power Platform solution design against all 5 pillars of the Power Platform Well-Architected Framework (Reliability, Security, Operational Excellence, Performance Efficiency, Experience Optimization). Accepts a target audience (exec, dev, architect, opsec) and a proposed design description. Produces an interactive HTML evaluation artifact tailored to the audience."
---

# Power Platform Well-Architected Evaluation Skill

## Purpose
Evaluate a proposed Power Platform solution design against the **5 pillars** of the Power Platform Well-Architected Framework and produce a professional, interactive HTML evaluation artifact.

## Parameters (extract from user message)
1. **Target audience** — one of:
   - `exec` → Executive Summary format
   - `dev` → Developer-focused with technical remediation guidance
   - `architect` → Most comprehensive, full checklist deep-dive for Solution Architects
   - `opsec` → Security & compliance focused for security professionals
2. **Proposed design** — free-text description of the Power Platform solution

If the user omits the audience, ask which one using `m_ask_user` with the four options. If the user omits the design, ask them to describe it.

---

## Step 1 — Gather Framework Data

Fetch **all 5 checklist files** from the GitHub repo in parallel:

```
https://raw.githubusercontent.com/MicrosoftDocs/power-platform/main/power-platform/well-architected/reliability/checklist.md
https://raw.githubusercontent.com/MicrosoftDocs/power-platform/main/power-platform/well-architected/security/checklist.md
https://raw.githubusercontent.com/MicrosoftDocs/power-platform/main/power-platform/well-architected/operational-excellence/checklist.md
https://raw.githubusercontent.com/MicrosoftDocs/power-platform/main/power-platform/well-architected/performance-efficiency/checklist.md
https://raw.githubusercontent.com/MicrosoftDocs/power-platform/main/power-platform/well-architected/experience-optimization/checklist.md
```

Parse each checklist to extract the **Code** (e.g., RE:01, SE:05) and **Recommendation** text from the markdown tables.

## Step 2 — Gather Technical Context

Use the Microsoft Learn MCP tools to search for technical documentation relevant to the proposed design. Run **at least 3 targeted searches** using `ms_learn-microsoft_docs_search` covering:

1. **Platform limits and constraints** relevant to the design (e.g., delegation limits, row size limits, connector throttling, API call limits, column limits, license requirements)
2. **Performance best practices** for the specific Power Platform components mentioned (canvas apps, model-driven apps, Power Automate flows, Dataverse, connectors, PCF controls, etc.)
3. **Security and governance** for the specific scenario (DLP policies, environment strategy, data classification, role-based access, column-level security)
4. **Architecture patterns** relevant to the scenario (responsive design, offline capability, ALM, data modeling)

Use `ms_learn-microsoft_docs_fetch` to get full content from any highly relevant pages found in search results.
Use `ms_learn-microsoft_code_sample_search` when the design involves custom code, PCF controls, or specific APIs.

Extract concrete facts: specific numeric limits, documented warnings, recommended patterns, and anti-patterns.

## Step 3 — Requirements Gap Analysis

Before proceeding with the evaluation, review the proposed design description against the checklist data and technical context gathered so far. Identify whether the design description has gaps that would prevent a thorough, accurate evaluation.

Compile a list of clarifying questions for the user. Questions should cover topics such as:

- **Approach validation**: Does the selected approach make sense, or are there better Power Platform tools/components to achieve the same result? (e.g., canvas app vs. model-driven app vs. custom pages, Power Automate cloud flow vs. desktop flow vs. low-code plug-in)
- **Business process gaps**: Are there gaps in the business process description? A complete evaluation needs detailed steps, roles, decision points, and exception handling for each process the solution supports.
- **Technical requirements gaps**: Are there missing technical requirements such as data volumes, concurrent user counts, integration endpoints, offline needs, or data migration scope?
- **Non-functional requirements**: Are non-functional requirements defined — such as availability targets (SLA), response time expectations, data retention policies, disaster recovery/backup requirements, or accessibility standards?
- **Must-have constraints**: Are there organizational must-haves — a specific way of working (e.g., ALM/DevOps practices), security policies (e.g., conditional access, DLP), compliance standards (e.g., SOC 2, GDPR, HIPAA), or existing platform governance rules that the solution must respect?
- **Licensing restrictions**: Is there a restriction on the type of licenses available or budgeted for the solution? (e.g., must stay within standard/seeded licenses, cannot use premium connectors, limited AI Builder credits, specific Dynamics 365 entitlements already owned)

### Presenting questions to the user

Present the questions to the user in a clear, numbered format. Only ask questions where the provided design description is genuinely missing information — do not ask about topics already well-covered.

At the end of the question list, inform the user:
> **You can respond with "Assume Defaults"** if you'd like me to proceed using best-judgement assumptions for all unanswered questions.

### Handling the response

- **If the user answers specific questions**: Incorporate their answers into the design context for the remaining steps.
- **If the user responds with "Assume Defaults"**: Answer every question yourself using best judgement based on the design description, the technical context gathered in Step 2, and industry best practices. Document all assumptions clearly and include them in the final HTML artifact under an "Assumptions" section so the user can review what was assumed.
- **If the user provides a mix** (answers some, says "assume defaults" for the rest): Honor explicit answers and assume defaults for the rest.

Proceed to Step 4 only after the user has responded or chosen "Assume Defaults".

---

## Step 4 — Evaluate Each Recommendation

For **every** recommendation code in **all 5 checklists**, assess the proposed design and assign a verdict:

- **PASS** — Design aligns with the recommendation
- **WARN** — Potential concern; design is incomplete or ambiguous on this point
- **FAIL** — Design clearly violates or ignores this recommendation
- **N/A** — Recommendation is not applicable to this design scope

For each non-PASS item, write a finding that includes:
- **What's wrong**: Specific issue tied to the design
- **Why it matters**: Impact on the workload (cite platform limits, documented behavior, or best practice references)
- **How to fix** (for `dev` and `architect` audiences): Concrete remediation steps with links to relevant Microsoft Learn documentation

### Scoring
Assign a numeric score per pillar (0–5 scale):
- 0–1: Critical — hard platform blockers or fundamental design flaws
- 1–2: Severe — major issues that will cause significant problems
- 2–3: Concerning — notable gaps that need attention before production
- 3–4: Adequate — minor improvements recommended
- 4–5: Strong — well-aligned with framework guidance

Overall score = weighted average of all 5 pillars (equal weight).

---

## Step 5 — Format Output by Audience

### All audiences
Generate a **self-contained interactive HTML file** saved to the workspace directory using the **web-artifacts-builder skill theme** (Clawpilot theme variables, Segoe UI font, rose accent, etc.). The HTML must include:
- Theme detection script and full CSS variable block (light + dark mode)
- The proposed design description at the top
- If "Assume Defaults" was used (fully or partially), an **Assumptions** section listing every assumption made and the rationale behind it
- Overall score banner with visual indicator (color-coded circle)
- Per-pillar scores

### Audience-specific content

#### `exec` — Executive Summary
- **Banner**: Overall score + one-sentence verdict
- **Pillar summary table**: 5 rows, columns for Pillar | Score | Status | One-line finding
- **Top risks**: 3–5 bullet points, plain business language, no jargon
- **Recommendations**: 3–5 strategic actions, phrased as business decisions
- **No individual checklist items** — keep it to one screen
- **Tone**: Concise, decision-oriented, risk-focused

#### `dev` — Developer Guidance
- **Banner**: Overall score + summary
- **Per-pillar tabs** with expandable checklist items (only FAIL and WARN items shown)
- **Each finding includes**:
  - The issue
  - Code-level or configuration-level fix with specifics (e.g., "enable Explicit Column Selection", "use `ShowColumns()` to limit retrieved fields", "set data row limit to 1 for testing delegation")
  - Links to relevant Microsoft Learn pages
- **Remediation priority list**: Ordered by impact, with effort estimates (low/medium/high)
- **Code snippets or formula examples** where applicable (Power Fx, JSON, YAML)
- **Tone**: Practical, hands-on, solution-oriented

#### `architect` — Comprehensive Architecture Review
- **Banner**: Overall score + summary
- **Per-pillar tabs** with ALL checklist items (PASS, WARN, FAIL, N/A)
- **Each item fully explained** with:
  - Assessment rationale
  - Platform limits and documented constraints (with specific numbers)
  - Architecture tradeoffs
  - Recommended patterns and alternatives
  - Cross-references to related checklist items in other pillars
- **Architecture recommendations section**:
  - Data model recommendations (with suggested table structure if applicable)
  - Component selection guidance (canvas vs. model-driven vs. custom pages vs. PCF)
  - Environment and ALM strategy considerations
  - Integration pattern recommendations
- **Tradeoff analysis**: How addressing one pillar might impact others
- **Tone**: Thorough, analytical, pattern-oriented

#### `opsec` — Security & Compliance Focus
- **Banner**: Overall security posture + summary
- **Security pillar shown first and in full detail** (all SE:xx items with full findings)
- **Other pillars shown in condensed form** — only security-relevant items from RE, OE, PE, XO (e.g., RE:05 error handling that could leak data, OE:06 monitoring for threat detection, PE:08 data exposure through over-fetching)
- **Threat summary**: Key threat vectors identified in the design
- **Compliance considerations**: DLP policy implications, data residency, audit requirements
- **Security recommendations**:
  - Identity and access model
  - Data classification and column-level security
  - Environment segmentation
  - Monitoring and incident response
- **Risk matrix**: Likelihood × Impact for top security findings
- **Tone**: Risk-focused, controls-oriented, compliance-aware

---

## Step 6 — Deliver

1. Save the HTML artifact to the workspace directory as `well-architected-evaluation.html`
2. Display it inline using markdown image syntax
3. Provide a brief summary in chat text (3–5 sentences) highlighting the overall score and top findings

---

## Important Notes

- **Always fetch the latest checklists from GitHub** — do not rely on memorized checklist content. The framework is updated periodically.
- **Always use Microsoft Learn MCP tools** for technical backing — cite specific limits, documented behavior, and official guidance. Do not guess at platform limits.
- **Be specific**: Instead of "this might be slow", say "Canvas apps page data in 100-record increments; with N columns at ~X bytes each, each page request transfers approximately Y KB, exceeding typical mobile payload recommendations."
- **Link to sources**: For `dev` and `architect` audiences, include Learn URLs for every major finding.
- **The HTML must be self-contained**: No external dependencies. All CSS/JS inline. Must work when opened directly in a browser.
- **Use the Clawpilot theme**: Follow the web-artifacts-builder skill theme requirements exactly (CSS variables, font, colors, border-radius, spacing).
