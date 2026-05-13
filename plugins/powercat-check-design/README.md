# powercat-check-design

A Copilot CLI plugin that evaluates a Power Platform solution design against the **Power Platform Well-Architected Framework** and produces an interactive HTML evaluation artifact tailored to a target audience.

## Skills

- **powercat-check-design** — Evaluates a proposed Power Platform solution design against all 5 pillars (Reliability, Security, Operational Excellence, Performance Efficiency, Experience Optimization) and produces an audience-specific HTML report (`exec`, `dev`, `architect`, or `opsec`).

## Usage

After `copilot plugin install powercat-check-design@powercat-marketplace`, ask Copilot things like:

> Review my Power Platform design as an architect: [describe the solution]

> Give me an executive summary of this Power Platform design: [describe the solution]

> Do a security review of this Power Platform design: [describe the solution]

The skill will:

1. Fetch the latest Well-Architected checklists from the Microsoft Docs GitHub repo.
2. Pull supporting platform documentation via the Microsoft Learn MCP tools.
3. Ask clarifying questions about any gaps in the design (or assume defaults if requested).
4. Score each pillar 0–5 and call out PASS / WARN / FAIL / N/A items.
5. Produce a self-contained, themed HTML report saved to the workspace.
