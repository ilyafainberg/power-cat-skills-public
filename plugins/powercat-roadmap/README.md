# powercat-roadmap

A Copilot CLI plugin that generates a customer-facing PowerPoint deck summarizing the key roadmap features for one or more Microsoft Power Platform or Dynamics 365 products, based on the latest **Microsoft release plan** (sourced live from Microsoft Learn).

## Skills

- **powercat-roadmap** — Takes a comma-separated list of products (for example, `Power Apps, Power Automate, Copilot Studio`) and produces a designed `.pptx` deck that highlights new Public Preview and General Availability features for the current release wave, grouped by product and theme.

## Usage

After `copilot plugin install powercat-roadmap@powercat-marketplace`, ask Copilot things like:

> Build a roadmap deck for Power Apps, Power Automate, Copilot Studio.

> Create a customer-facing roadmap overview for Power Pages and Dataverse.

The skill will:

1. Verify the Microsoft Learn MCP server is connected (and offer to connect it if missing).
2. Fetch the latest release-plan pages for each product from `learn.microsoft.com`.
3. Group features into **🆕 New in Preview**, **✅ Reaching General Availability**, and **📋 Coming Soon**.
4. Generate a designed `.pptx` deck (using the `pptx` skill / PptxGenJS) with a title slide, agenda, per-product intro and feature highlight slides, a summary, and a resources slide.
5. QA the deck (text extraction + visual inspection) and save it to the working directory as `{Product1} {Product2} Roadmap Overview.pptx`.

## Notes

- Always uses the **latest release wave** available on Microsoft Learn.
- Includes a disclaimer that delivery dates are subject to change per Microsoft policy.
- Customer-facing tone — frames each feature in terms of business value, not internal jargon.
