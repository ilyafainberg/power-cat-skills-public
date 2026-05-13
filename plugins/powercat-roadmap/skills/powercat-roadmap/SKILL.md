---
name: "powercat-roadmap"
description: "Creates a customer-facing PPTX overview of key Microsoft roadmap features from a comma-separated product list. Emphasizes new previews and GA features. Checks and connects the Microsoft Learn MCP server if missing."
---

# Roadmap Overview Deck Skill

## Input
The user provides a **comma-separated list of product names** (e.g. "Power Apps, Power Automate, Copilot Studio").

## Step 0 — Ensure Microsoft Learn MCP Server

Before doing anything else, verify the Microsoft Learn MCP server is available:

1. Check if the tools `ms_learn-microsoft_docs_search` and `ms_learn-microsoft_docs_fetch` are available by attempting a simple search call (e.g. search for "Power Platform release plan").
2. If the MCP tools are NOT available or return connection errors:
   - Tell the user: "The Microsoft Learn MCP server is not connected. I'll connect it now."
   - Use Playwright browser automation to open Clawpilot settings and add the MCP server:
     - URL: `https://learn.microsoft.com/api/mcp`
     - This is a remote MCP server (SSE type)
   - Alternatively, instruct the user to add it manually if browser automation isn't feasible:
     - "Please add the MCP server `https://learn.microsoft.com/api/mcp` in your Clawpilot MCP settings, then retry."
   - Wait for confirmation before proceeding.
3. If the tools ARE available, proceed to Step 1.

## Step 1 — Research

For **each product** in the comma-separated list:

1. Use `ms_learn-microsoft_docs_search` to search for: `"{product name}" 2026 wave 1 release plan planned features`
2. Use `ms_learn-microsoft_docs_fetch` to fetch the full planned features page. The URL pattern is:
   `https://learn.microsoft.com/power-platform/release-plan/2026wave1/{product-slug}/planned-features`
   Common slugs: `power-apps`, `power-automate`, `power-pages`, `microsoft-copilot-studio`, `data-platform`
   For Dynamics 365 products, the base changes to: `https://learn.microsoft.com/dynamics365/release-plan/2026wave1/...`
3. Also fetch the product overview page:
   `https://learn.microsoft.com/power-platform/release-plan/2026wave1/{product-slug}/`
4. Extract from the fetched pages:
   - The **investment themes** (from the overview page)
   - Every feature listed in tables, noting:
     - Feature name
     - 1 Paragraph description of the feature
     - Whether it's in **Public Preview** (with date) or reaching **General Availability** (with date)
     - Whether it's already released (has a checkmark ✓)
   - Group features by their section headers (e.g. "Cloud flows", "Desktop flows", "Building modern apps")

**IMPORTANT**: Always use the latest wave. As of now that is 2026 Wave 1 (April–September 2026). If results seem outdated, also try searching for the current/latest wave.

## Step 2 — Organize Content

For each product, organize features into categories:

1. **🆕 New in Preview** — features entering public preview this wave
2. **✅ Reaching General Availability** — features moving to GA this wave
3. **📋 Coming Soon** — features announced but not yet in preview

Prioritize features that are most impactful for customers. Drop minor or admin-only features if the deck would exceed ~15 slides total.

## Step 3 — Build the PPTX

Use the pptx skill approach (PptxGenJS via Node.js). Load the skill instructions first with `m_get_skill("pptx")` and follow its pptxgenjs.md guide.

### Deck Structure

1. **Title slide** — "Product Roadmap Overview — 2026 Wave 1" with subtitle listing the products and date range (Apr–Sep 2026)
2. **Agenda/Overview slide** — list of products covered with a one-line summary per product
3. **Per product** (repeat for each product):
   a. **Product intro slide** — product name as hero, key investment themes as 3–4 visual cards or icon+text blocks
   b. **Feature highlight slides** (1–3 per product depending on feature count):
      - Group by theme or category
      - Use a **table or card layout** showing feature name, status badge (Preview / GA / Coming Soon), and target date
      - Visually emphasize GA features (they're ready to use!) and new previews (try them now!)
   c. **Key dates slide** (optional, if dates are interesting) — timeline or calendar visual
4. **Summary / Next Steps slide** — key takeaways, links to official release plans
5. **Resources slide** — URLs to the official release plan pages

### Design Requirements

- Choose a **bold, professional color palette** appropriate for a Microsoft technology audience — e.g. blues, teals, or a modern dark theme. Do NOT use default white+black.
- Use **status badges/colors**: Green for GA, Blue for Preview, Gray for Coming Soon
- Include visual elements on every slide — icons, shapes, cards. No text-only slides.
- Use **Georgia or Trebuchet MS** for headers, **Calibri** for body text
- Follow all PptxGenJS rules from the pptx skill (no # in colors, no unicode bullets, breakLine:true, fresh option objects, etc.)

### Build Script

Write a single Node.js script that generates the deck. Use `$env:NODE_PATH = (npm root -g)` before running. Save the output as `{Product1} {Product2} Roadmap Overview.pptx` in the current working directory.

## Step 4 — QA

Follow the pptx skill QA process:
1. Extract text with `python -m markitdown output.pptx` to verify content
2. Convert to images and visually inspect using a subagent
3. Fix any issues found (overlaps, cut-off text, alignment problems)
4. Re-verify after fixes

## Step 5 — Deliver

Present the finished file to the user with a brief summary of what's included:
- Number of slides
- Products covered
- Count of features highlighted
- Note any features that are especially noteworthy (e.g. major GA milestones)

## Important Notes

- **Timelines may change** — always include a disclaimer slide or footer noting that delivery dates are subject to change per Microsoft policy.
- **Keep it customer-facing** — avoid internal jargon. Use plain language that a business decision-maker would understand.
- **Emphasize value** — for each feature, frame it in terms of what it enables for the customer, not just what the feature does technically.
