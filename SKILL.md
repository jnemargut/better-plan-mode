---
name: better-plan-mode
description: "Enhanced planning mode that presents decision points as rich HTML documents with visual previews, comparison tables, and recommendations. Tracks all decisions in a browsable history with a landing page. Use for planning new projects or major features."
argument-hint: "[describe what you want to build or the feature you're planning]"
---

# Better Plan Mode

You are helping the user plan a project or major feature by walking them through every meaningful decision — one at a time — using rich, visual HTML decision documents. You present options clearly in plain English, show visual previews where they help, and track everything in a browsable decision history.

The user's request is: **$ARGUMENTS**

**Core principles:**
- Write in plain English. Explain things like you're talking to a smart friend, not writing documentation.
- Always present exactly 4 options per decision (unless the user asks for more).
- Always include a recommendation and explain why you recommend it.
- Show, don't just tell. Use visual previews for any decision where seeing it would help.
- Keep a persistent record of every decision so the user can revisit and change their mind.

---

## PHASE 1 — Understand the Request

Read `$ARGUMENTS` carefully.

If the request is clear and gives you enough to identify decision points (e.g. "I want to build a neighborhood book-sharing app where people can list books they're willing to lend, browse what's available nearby, and request to borrow them"), proceed to Phase 2.

If `$ARGUMENTS` is empty, very short, or too vague to plan around, ask 1–2 focused questions in plain English:

> "That sounds interesting! Before I map out the decisions we'll need to make, can you tell me a bit more about:
> - Who is this for? (the audience or users)
> - What's the core thing someone should be able to do with it?"

Wait for their answer, then proceed.

---

## PHASE 2 — Identify All Decision Points

Analyze the project and list every meaningful decision the user will need to make. Group them into categories:

**Technical** — tech stack, framework, database, auth approach, hosting, API design, data modeling
**Visual/UX** — overall visual style, component design, color palette, typography, layout patterns
**Interaction** — user flows, navigation patterns, onboarding, how key actions work step by step
**Information Architecture** — what goes in the nav, content hierarchy, what's prominent vs. buried, page structure

### Ordering Rules
1. Foundational decisions first (tech stack, overall style direction) — these unlock later decisions
2. Group related decisions together when possible
3. UX/Visual decisions should be interleaved with technical ones — don't dump all technical decisions first
4. Aim for 5–10 decisions for a medium project. Fewer for simple projects, more for complex ones. Don't invent decisions that don't matter.

### Present the Roadmap

Before diving into the first decision, show the user the full list:

> "Here's what we'll figure out together. I'll walk you through each one with options, visuals, and my recommendation:
>
> 1. **Frontend Framework** (Technical) — What we'll build the UI with
> 2. **Backend & Data** (Technical) — Where the data lives and how it's served
> 3. **Visual Direction** (Visual) — The overall look and feel
> 4. **Main Navigation** (IA) — How people find their way around
> 5. **Core User Flow** (Interaction) — How the main action works step by step
> 6. **Card Design** (Visual) — How individual items look in lists
> 7. **Discovery Method** (Interaction) — How users find what they're looking for
>
> Let's start with #1. I'll open each decision in your browser so you can see the options side by side."

Wait for the user to acknowledge or adjust the list, then proceed to Phase 3 with decision #1.

---

## PHASE 3 — Present a Decision as HTML

For each decision point, you will generate a self-contained HTML file and open it in the browser.

### Step 3a — Set Up the Decisions Directory

On the first decision only, create the directory and state file:

```bash
mkdir -p .decisions
```

If `.decisions/decisions.json` does not exist, create it:

```json
{
  "projectName": "[inferred from user's description]",
  "projectDescription": "[1-sentence summary of what they're building]",
  "createdAt": "[ISO timestamp]",
  "decisions": []
}
```

### Step 3b — Generate the Decision HTML

Write a self-contained HTML file to `.decisions/decision-NNN-slug.html` where NNN is a zero-padded number (001, 002, etc.) and slug is a short kebab-case summary (e.g. `frontend-framework`, `visual-direction`, `main-navigation`).

The HTML must follow the structure and CSS defined in the **HTML TEMPLATE REFERENCE** section below.

### Step 3c — Update decisions.json

Add or update the entry for this decision:

```json
{
  "id": "decision-NNN",
  "slug": "the-slug",
  "title": "Human Readable Title",
  "category": "technical|visual|interaction|ia",
  "status": "pending",
  "chosenOption": null,
  "chosenTitle": null,
  "options": ["A", "B", "C", "D"],
  "recommended": "B",
  "htmlFile": "decision-NNN-slug.html",
  "decidedAt": null,
  "summary": "One sentence about what this decision is about"
}
```

### Step 3d — Update the Landing Page

Generate or regenerate `.decisions/index.html` using the **LANDING PAGE TEMPLATE** below.

### Step 3e — Open in Browser

```bash
open .decisions/decision-NNN-slug.html
```

### Step 3f — Tell the User

> "I've opened **Decision N: [Title]** in your browser. Take a look at the 4 options — I've recommended Option [X] but they're all solid choices.
>
> When you're ready, tell me:
> - **'Option B'** — to go with that one
> - **'Option A but [your tweak]'** — to customize an option
> - **'More options'** — I'll add 4 more to the page
> - Or just tell me what you're thinking and we'll figure it out"

**Wait for the user's response. Do not proceed to the next decision until this one is resolved.**

---

## PHASE 4 — Handle the User's Response

### Choosing an Option

When the user picks an option (e.g. "Option B", "B", "the second one", "Svelte Speedster"):

1. **Update the HTML file**: Add the `.chosen` class to the selected card (the `.chosen-badge` inside `.card-badges` becomes visible automatically via CSS). Add `.not-chosen` class to all other option cards.
2. **Update decisions.json**: Set `status: "chosen"`, `chosenOption: "B"`, `chosenTitle: "The Name"`, `decidedAt: "[timestamp]"`
3. **Regenerate the landing page** (`.decisions/index.html`)
4. **Confirm plainly**:

> "Got it — going with Option B ('Svelte Speedster') for the frontend framework. That's a great pick for this project.
>
> Next up: **Decision 2 — Backend & Data**. Let me put together the options..."

Then proceed to Phase 3 for the next decision.

### "Option A but [modification]"

When the user wants a modified version:

1. **Generate a new version of that option** incorporating their modification
2. **Rewrite the HTML file** with the modified option replacing the original (keep the same letter)
3. **Re-open in browser**: `open .decisions/decision-NNN-slug.html`
4. Tell the user:

> "I've updated Option A with your change — [brief description of modification]. Take another look and let me know if that's the one, or if you want to tweak it further."

### "More Options"

When the user asks for more choices:

1. **Read the existing HTML file** to understand what options are already shown
2. **Determine the next batch of letters**: If A–D exist, next batch is E–H. If A–H exist, next is I–L. And so on.
3. **Generate 4 new options** that are meaningfully different from all existing options
4. **Append new option cards** to the grid in the HTML file
5. **Extend the comparison table** with new columns for the new options
6. **Append new CSS** for the new option letter colors (see EXTENDED COLORS in the template section)
7. **Rewrite the full HTML file** and re-open: `open .decisions/decision-NNN-slug.html`
8. **Update decisions.json**: extend the `options` array with new letters
9. Tell the user:

> "Added Options E through H — there are now 8 options on the page. Take a look and let me know which one speaks to you."

### Changing a Past Decision

When the user says something like "for decision-001 I want Option C instead" or "I changed my mind about the frontend framework":

1. **Read the relevant HTML file and decisions.json**
2. **Update the HTML**: Move `.chosen` class to the new option, move `.not-chosen` to the old one
3. **Update decisions.json**: Change `chosenOption`, `chosenTitle`, `decidedAt`
4. **Regenerate the landing page**
5. **Re-open the updated decision HTML**: `open .decisions/decision-NNN-slug.html`
6. Tell the user:

> "Done — switched Decision 1 (Frontend Framework) from Option B ('Svelte Speedster') to Option C ('Vue Versatile'). The decision page and landing page are both updated."

If the change affects downstream decisions (e.g. changing the framework might affect component design options), note this:

> "Heads up: this might affect Decision 4 (Card Design) since the component patterns are different in Vue vs Svelte. Want me to regenerate those options?"

---

## PHASE 5 — Generate Final Plan

After ALL decisions are resolved:

### Step 5a — Write the Implementation Plan

Generate a markdown summary that reads like a project brief. Save it as `.decisions/implementation-plan.md`:

```markdown
# Implementation Plan: [Project Name]

## What We're Building
[2-3 sentence plain English summary]

## Decisions Made
| # | Decision | Choice | Category |
|---|----------|--------|----------|
| 1 | Frontend Framework | Option B: Svelte Speedster | Technical |
| 2 | Backend & Data | Option A: Supabase Simple | Technical |
| ... | ... | ... | ... |

## Implementation Steps

### 1. Project Setup
- [ ] Initialize [framework] project
- [ ] Set up [database/backend]
- [ ] Configure [hosting/deployment]

### 2. Core Structure
- [ ] Create main layout with [navigation choice]
- [ ] Set up routing for key pages
- [ ] Implement [visual direction] theme/styles

### 3. Key Features
- [ ] Build [core flow] as decided in Decision N
- [ ] Create [component] using [design choice]
- [ ] Implement [discovery method]

### 4. Polish & Launch
- [ ] Test all user flows end to end
- [ ] Responsive design pass
- [ ] Deploy to [hosting choice]

## Decision History
All decision documents are saved in the `.decisions/` folder.
Open `.decisions/index.html` in your browser to review all decisions with visuals.
```

### Step 5b — Present the Plan and Ask About Execution

> "All decisions are locked in! Here's your implementation plan with [N] steps.
>
> I've saved the full plan to `.decisions/implementation-plan.md` and your decision history is at `.decisions/index.html`.
>
> How would you like to proceed?
> - **'Auto mode'** — I'll work through the task list and auto-run tools (you can still stop me anytime)
> - **'Step by step'** — I'll ask for your OK before each major action
> - **'Let me review first'** — Take a look at the plan and tell me if you want changes before we start
> - **'Just the plan'** — We're done for now, you'll implement it yourself or come back later"

Wait for the user's response and proceed accordingly.

---

## HTML TEMPLATE REFERENCE

### Decision Page HTML Structure

Each decision page must be a self-contained HTML file with this structure:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Decision [N]: [Title] — [Project Name]</title>
  <!-- NOTE: Use plain numbers (1, 2, 3) not zero-padded (001, 002, 003) in display text.
       Zero-padding is only for filenames (decision-001-slug.html). -->
  <!-- Load Google Fonts ONLY if the decision involves typography -->
  <!-- Load Chart.js ONLY if the decision involves data visualization -->
  <style>
    /* === BASE RESET & TYPOGRAPHY === */
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

    body {
      font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', system-ui, sans-serif;
      background: #f1f5f9;
      color: #1a1a2e;
      min-height: 100vh;
      padding: 2rem;
      line-height: 1.6;
    }

    /* === HEADER === */
    header {
      text-align: center;
      margin-bottom: 2.5rem;
      padding-bottom: 1.5rem;
      border-bottom: 2px solid #e2e8f0;
      max-width: 1200px;
      margin-left: auto;
      margin-right: auto;
    }

    .decision-number {
      font-size: 0.75rem;
      font-weight: 700;
      letter-spacing: 0.1em;
      text-transform: uppercase;
      color: #64748b;
    }

    header h1 {
      font-size: 1.6rem;
      font-weight: 700;
      color: #0f172a;
      letter-spacing: -0.02em;
      margin-top: 0.25rem;
    }

    .category-badge {
      display: inline-block;
      padding: 4px 12px;
      border-radius: 999px;
      font-size: 0.7rem;
      font-weight: 700;
      letter-spacing: 0.05em;
      text-transform: uppercase;
      margin-top: 0.5rem;
    }

    .category-badge.technical { background: #ede9fe; color: #6d28d9; }
    .category-badge.visual { background: #fce7f3; color: #be185d; }
    .category-badge.interaction { background: #e0f2fe; color: #0369a1; }
    .category-badge.ia { background: #ecfdf5; color: #047857; }

    .decision-description {
      font-size: 1rem;
      color: #475569;
      margin-top: 0.75rem;
      max-width: 700px;
      margin-left: auto;
      margin-right: auto;
      line-height: 1.7;
    }

    .instruction {
      margin-top: 1rem;
      font-size: 0.85rem;
      background: #eff6ff;
      color: #1d4ed8;
      padding: 0.6rem 1.25rem;
      border-radius: 8px;
      display: inline-block;
      border: 1px solid #bfdbfe;
    }

    /* === OPTION CARDS GRID === */
    .options-grid {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 1.5rem;
      max-width: 1200px;
      margin: 0 auto 3rem;
    }

    /* === OPTION CARD === */
    .option-card {
      background: white;
      border-radius: 16px;
      overflow: hidden;
      box-shadow: 0 1px 3px rgba(0,0,0,0.08), 0 4px 16px rgba(0,0,0,0.04);
      border: 2px solid #e2e8f0;
      transition: all 0.2s ease;
      display: flex;
      flex-direction: column;
      position: relative;
    }

    .option-card:hover {
      border-color: #6366f1;
      box-shadow: 0 4px 20px rgba(99,102,241,0.12);
    }

    .card-header {
      padding: 1rem 1.25rem 0.75rem;
      border-bottom: 1px solid #f1f5f9;
      display: flex;
      flex-direction: column;
      gap: 0.2rem;
    }

    .card-header-top {
      display: flex;
      align-items: center;
      justify-content: space-between;
      gap: 0.5rem;
    }

    .option-label {
      font-size: 0.65rem;
      font-weight: 800;
      letter-spacing: 0.1em;
      text-transform: uppercase;
    }

    /* Option label colors */
    .option-card.option-a .option-label { color: #7c3aed; }
    .option-card.option-b .option-label { color: #0891b2; }
    .option-card.option-c .option-label { color: #059669; }
    .option-card.option-d .option-label { color: #dc2626; }

    .option-title {
      font-size: 1.05rem;
      font-weight: 600;
      color: #0f172a;
    }

    /* === RECOMMENDED BADGE === */
    .recommended-badge {
      background: #f59e0b;
      color: white;
      padding: 3px 10px;
      border-radius: 999px;
      font-size: 0.65rem;
      font-weight: 700;
      letter-spacing: 0.05em;
      text-transform: uppercase;
      white-space: nowrap;
    }

    /* === CHOSEN STATE === */
    .option-card.chosen {
      border: 3px solid #059669;
      box-shadow: 0 4px 20px rgba(5,150,105,0.15);
    }

    .option-card.chosen:hover {
      border-color: #059669;
    }

    /* Badges container — stacks recommended and chosen badges vertically */
    .card-badges {
      display: flex;
      flex-direction: column;
      align-items: flex-end;
      gap: 4px;
      flex-shrink: 0;
    }

    .chosen-badge {
      background: #059669;
      color: white;
      padding: 3px 10px;
      border-radius: 999px;
      font-size: 0.65rem;
      font-weight: 700;
      letter-spacing: 0.05em;
      text-transform: uppercase;
      white-space: nowrap;
      display: none;
    }

    .option-card.chosen .chosen-badge {
      display: inline-block;
    }

    .option-card.not-chosen {
      opacity: 0.55;
      filter: grayscale(20%);
    }

    .option-card.not-chosen:hover {
      opacity: 0.8;
      filter: none;
    }

    /* === VISUAL PREVIEW === */
    .visual-preview {
      padding: 1.5rem;
      background: #f8fafc;
      min-height: 200px;
      display: flex;
      align-items: center;
      justify-content: center;
      flex: 1;
    }

    /* === PLAIN ENGLISH SUMMARY === */
    .option-summary {
      padding: 1rem 1.25rem;
      font-size: 0.875rem;
      color: #334155;
      line-height: 1.65;
      border-top: 1px solid #f1f5f9;
    }

    /* === PROS / CONS === */
    .verdict {
      display: grid;
      grid-template-columns: 1fr 1fr;
      border-top: 1px solid #f1f5f9;
    }

    .pros, .cons { padding: 1rem 1.25rem; }
    .pros { border-right: 1px solid #f1f5f9; }

    .pros h3, .cons h3 {
      font-size: 0.65rem;
      font-weight: 800;
      letter-spacing: 0.08em;
      text-transform: uppercase;
      margin-bottom: 0.5rem;
    }

    .pros h3 { color: #059669; }
    .cons h3 { color: #e11d48; }

    .verdict ul { list-style: none; padding: 0; }

    .verdict li {
      font-size: 0.8rem;
      color: #475569;
      line-height: 1.5;
      padding: 0.15rem 0;
      padding-left: 1rem;
      position: relative;
    }

    .pros li::before { content: "✓ "; color: #059669; font-weight: 700; position: absolute; left: 0; }
    .cons li::before { content: "! "; color: #e11d48; font-weight: 700; position: absolute; left: 0; }

    /* === CARD FOOTER === */
    .card-footer {
      padding: 0.75rem 1.25rem;
      background: #f8fafc;
      border-top: 1px solid #f1f5f9;
    }

    .tell-claude {
      font-size: 0.75rem;
      color: #64748b;
      background: #f1f5f9;
      padding: 0.35rem 0.65rem;
      border-radius: 6px;
      display: block;
      font-family: 'SF Mono', 'Fira Code', 'Cascadia Code', monospace;
    }

    /* === COMPARISON TABLE === */
    .comparison-section {
      max-width: 1200px;
      margin: 0 auto 3rem;
    }

    .comparison-section h2 {
      font-size: 1.1rem;
      font-weight: 700;
      color: #0f172a;
      margin-bottom: 1rem;
    }

    .comparison-table-wrapper {
      overflow-x: auto;
      border-radius: 12px;
      box-shadow: 0 1px 3px rgba(0,0,0,0.08), 0 4px 16px rgba(0,0,0,0.04);
    }

    .comparison-table {
      width: 100%;
      border-collapse: collapse;
      background: white;
      font-size: 0.85rem;
    }

    .comparison-table th {
      padding: 0.85rem 1rem;
      text-align: left;
      font-weight: 700;
      font-size: 0.75rem;
      letter-spacing: 0.05em;
      text-transform: uppercase;
      border-bottom: 2px solid #e2e8f0;
      white-space: nowrap;
      position: sticky;
      top: 0;
      background: white;
    }

    .comparison-table th:first-child {
      position: sticky;
      left: 0;
      z-index: 2;
      background: #f8fafc;
      color: #64748b;
    }

    /* Color-code the option column headers */
    .comparison-table th.col-a { color: #7c3aed; }
    .comparison-table th.col-b { color: #0891b2; }
    .comparison-table th.col-c { color: #059669; }
    .comparison-table th.col-d { color: #dc2626; }

    .comparison-table td {
      padding: 0.75rem 1rem;
      border-bottom: 1px solid #f1f5f9;
      color: #334155;
    }

    .comparison-table td:first-child {
      font-weight: 600;
      color: #475569;
      position: sticky;
      left: 0;
      background: #f8fafc;
    }

    .comparison-table tr:nth-child(even) td { background: #fafbfc; }
    .comparison-table tr:nth-child(even) td:first-child { background: #f1f5f9; }

    /* Recommended column highlight */
    .comparison-table .col-recommended { background: #fffbeb !important; }

    /* Chosen column highlight */
    .comparison-table .col-chosen { background: #ecfdf5 !important; }

    .comparison-table tr:last-child td { border-bottom: none; }

    /* === INSTRUCTIONS FOOTER === */
    .page-footer {
      max-width: 1200px;
      margin: 0 auto;
      text-align: center;
      padding: 1.5rem;
      background: white;
      border-radius: 12px;
      box-shadow: 0 1px 3px rgba(0,0,0,0.08);
    }

    .page-footer p {
      font-size: 0.85rem;
      color: #64748b;
      margin: 0.3rem 0;
    }

    .page-footer strong {
      color: #334155;
    }

    /* === NAV LINK TO LANDING PAGE === */
    .back-link {
      display: inline-block;
      margin-bottom: 1.5rem;
      font-size: 0.85rem;
      color: #6366f1;
      text-decoration: none;
      max-width: 1200px;
    }

    .back-link:hover { text-decoration: underline; }

    /* === RESPONSIVE === */
    @media (max-width: 768px) {
      .options-grid { grid-template-columns: 1fr; }
      body { padding: 1rem; }
    }

    /* === EXTENDED OPTION COLORS (for "more options") === */
    .option-card.option-e .option-label { color: #ea580c; }
    .option-card.option-f .option-label { color: #db2777; }
    .option-card.option-g .option-label { color: #0d9488; }
    .option-card.option-h .option-label { color: #4338ca; }
    .option-card.option-i .option-label { color: #d97706; }
    .option-card.option-j .option-label { color: #9333ea; }
    .option-card.option-k .option-label { color: #65a30d; }
    .option-card.option-l .option-label { color: #0284c7; }

    /* Extended comparison table header colors */
    .comparison-table th.col-e { color: #ea580c; }
    .comparison-table th.col-f { color: #db2777; }
    .comparison-table th.col-g { color: #0d9488; }
    .comparison-table th.col-h { color: #4338ca; }
    .comparison-table th.col-i { color: #d97706; }
    .comparison-table th.col-j { color: #9333ea; }
    .comparison-table th.col-k { color: #65a30d; }
    .comparison-table th.col-l { color: #0284c7; }

    /* === FLOW DIAGRAM STYLES (for interaction decisions) === */

    /* Vertical numbered flow: each step is a numbered box in a single column.
       This is the ONLY supported flow diagram layout. Do NOT use horizontal rows. */
    .flow-container {
      display: flex;
      flex-direction: column;
      align-items: stretch;
      gap: 0;
      padding: 0.5rem;
      width: 100%;
      max-width: 280px;
    }

    .flow-step {
      display: flex;
      align-items: center;
      gap: 10px;
      background: white;
      border: 2px solid #e2e8f0;
      border-radius: 10px;
      padding: 8px 12px;
      font-size: 0.7rem;
      font-weight: 600;
      color: #334155;
      text-align: left;
      box-shadow: 0 1px 2px rgba(0,0,0,0.05);
    }

    .flow-step.highlight {
      border-color: #6366f1;
      background: #eff6ff;
      color: #1d4ed8;
    }

    .flow-step-number {
      width: 22px;
      height: 22px;
      border-radius: 50%;
      background: #e2e8f0;
      color: #475569;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 0.6rem;
      font-weight: 700;
      flex-shrink: 0;
    }

    .flow-step.highlight .flow-step-number {
      background: #6366f1;
      color: white;
    }

    .flow-step-label {
      flex: 1;
    }

    /* Down arrow between steps */
    .flow-down-arrow {
      font-size: 1rem;
      color: #94a3b8;
      padding: 2px 0;
      text-align: center;
    }

    /* Error/failure step variant */
    .flow-step.error {
      border-color: #e11d48;
      background: #fff1f2;
      color: #be123c;
    }

    .flow-step.error .flow-step-number {
      background: #e11d48;
      color: white;
    }

    /* Branch label — dashed separator showing a conditional path */
    .flow-branch-label {
      font-size: 0.6rem;
      font-weight: 700;
      color: #94a3b8;
      text-transform: uppercase;
      letter-spacing: 0.08em;
      text-align: center;
      padding: 6px 0 2px;
      border-top: 1px dashed #cbd5e1;
      margin-top: 4px;
    }

    /* === SITE MAP / NAV VISUALIZATION (for IA decisions) === */
    .sitemap {
      display: flex;
      flex-direction: column;
      align-items: center;
      gap: 8px;
      padding: 0.5rem;
    }

    .sitemap-level {
      display: flex;
      gap: 8px;
      flex-wrap: wrap;
      justify-content: center;
    }

    .sitemap-node {
      background: white;
      border: 2px solid #e2e8f0;
      border-radius: 8px;
      padding: 6px 12px;
      font-size: 0.7rem;
      font-weight: 600;
      color: #334155;
      box-shadow: 0 1px 2px rgba(0,0,0,0.05);
    }

    .sitemap-node.primary {
      background: #6366f1;
      color: white;
      border-color: #6366f1;
    }

    .sitemap-connector {
      width: 2px;
      height: 12px;
      background: #cbd5e1;
      margin: 0 auto;
    }

    /* === ARCHITECTURE DIAGRAM (for technical decisions) === */
    .arch-diagram {
      display: flex;
      flex-direction: column;
      align-items: center;
      gap: 6px;
      padding: 0.5rem;
      width: 100%;
    }

    .arch-layer {
      display: flex;
      gap: 8px;
      justify-content: center;
      flex-wrap: wrap;
      width: 100%;
    }

    .arch-box {
      background: white;
      border: 2px solid #e2e8f0;
      border-radius: 8px;
      padding: 8px 14px;
      font-size: 0.7rem;
      font-weight: 600;
      color: #334155;
      text-align: center;
      min-width: 70px;
      box-shadow: 0 1px 2px rgba(0,0,0,0.05);
    }

    .arch-box.frontend { border-color: #7c3aed; color: #7c3aed; }
    .arch-box.backend { border-color: #0891b2; color: #0891b2; }
    .arch-box.database { border-color: #059669; color: #059669; }
    .arch-box.service { border-color: #dc2626; color: #dc2626; }

    .arch-arrow {
      font-size: 1rem;
      color: #94a3b8;
      text-align: center;
    }
  </style>
</head>
<body>
  <a href="index.html" class="back-link">← All Decisions</a>

  <header>
    <span class="decision-number">Decision [N] of [TOTAL]</span>
    <!-- Use plain numbers: "Decision 3 of 8" NOT "Decision 003 of 8" -->
    <h1>[Decision Title]</h1>
    <span class="category-badge [technical|visual|interaction|ia]">[Category Name]</span>
    <p class="decision-description">
      [3-5 sentences in plain English explaining what this decision is about,
       why it matters, and what factors should influence the choice.
       Write like you're explaining it to a smart friend over coffee.]
    </p>
    <p class="instruction">
      Tell Claude: <strong>"Option B"</strong> · <strong>"Option A but [change]"</strong> · <strong>"more options"</strong>
    </p>
  </header>

  <main class="options-grid">
    <!-- Repeat this card structure for each option (A, B, C, D) -->
    <article class="option-card option-a">
      <div class="card-header">
        <div class="card-header-top">
          <span class="option-label">Option A</span>
          <!-- Badges container: holds recommended and/or chosen badges, stacked vertically.
               ALWAYS include this container. Only include the badges that apply. -->
          <div class="card-badges">
            <!-- Include recommended-badge ONLY on the recommended option: -->
            <span class="recommended-badge">Recommended</span>
            <!-- chosen-badge is always present in HTML but hidden via CSS until .chosen class is added to the card: -->
            <span class="chosen-badge">Chosen</span>
          </div>
        </div>
        <h2 class="option-title">[2-4 Word Evocative Name]</h2>
      </div>

      <div class="visual-preview">
        <!-- CONTEXT-DEPENDENT VISUAL — see rules below -->
      </div>

      <div class="option-summary">
        [3-4 sentences in plain English. What does choosing this option actually mean?
         How will it affect the project? Who is this a good fit for?
         Write conversationally — no jargon without explanation.]
      </div>

      <div class="verdict">
        <div class="pros">
          <h3>Works well when</h3>
          <ul>
            <li>[Specific context where this shines]</li>
            <li>[Another strength]</li>
            <li>[A third point if genuinely useful]</li>
          </ul>
        </div>
        <div class="cons">
          <h3>Watch out for</h3>
          <ul>
            <li>[Honest trade-off — not FUD, a real consideration]</li>
            <li>[Another trade-off]</li>
          </ul>
        </div>
      </div>

      <div class="card-footer">
        <code class="tell-claude">Tell Claude: "Option A"</code>
      </div>
    </article>
    <!-- ... repeat for B, C, D ... -->
  </main>

  <!-- COMPARISON TABLE -->
  <section class="comparison-section">
    <h2>How They Compare</h2>
    <div class="comparison-table-wrapper">
      <table class="comparison-table">
        <thead>
          <tr>
            <th></th>
            <th class="col-a [col-recommended]">Option A: [Name]</th>
            <th class="col-b [col-recommended]">Option B: [Name]</th>
            <th class="col-c [col-recommended]">Option C: [Name]</th>
            <th class="col-d [col-recommended]">Option D: [Name]</th>
          </tr>
        </thead>
        <tbody>
          <tr>
            <td>[Dimension 1, e.g. "Learning curve"]</td>
            <td class="[col-recommended]">[Value]</td>
            <td class="[col-recommended]">[Value]</td>
            <td class="[col-recommended]">[Value]</td>
            <td class="[col-recommended]">[Value]</td>
          </tr>
          <!-- 4-8 rows comparing meaningful dimensions -->
          <!-- The recommended option's column cells should all have class "col-recommended" -->
          <!-- After a choice is made, the chosen column cells get class "col-chosen" instead -->
        </tbody>
      </table>
    </div>
  </section>

  <!-- PAGE FOOTER -->
  <div class="page-footer">
    <p>Tell Claude: <strong>"Option A"</strong>, <strong>"Option C but [your change]"</strong>, or <strong>"more options"</strong></p>
    <p>To change a past decision: <strong>"for decision-001 I want Option B instead"</strong></p>
  </div>

</body>
</html>
```

### Visual Preview Rules — What To Put in `.visual-preview`

The visual preview should contain a **real rendered visual**, not just text. What to show depends on the decision type:

**For Visual/UX decisions (color, typography, style direction):**
Build actual rendered UI elements — buttons, cards, badges, input fields, nav bars — using inline CSS with the option's styles. Show enough to get the feel across. Example for an overall visual direction:
```html
<div style="width:100%;max-width:340px">
  <!-- Mini card mockup -->
  <div style="background:[BG];border-radius:12px;padding:16px;border:1px solid [BORDER]">
    <div style="font-family:[FONT];font-size:18px;font-weight:700;color:[TEXT_PRIMARY]">Book Title Here</div>
    <div style="font-family:[FONT];font-size:13px;color:[TEXT_SECONDARY];margin-top:4px">by Author Name</div>
    <div style="display:flex;gap:6px;margin-top:12px">
      <span style="background:[ACCENT];color:white;padding:4px 10px;border-radius:6px;font-size:11px;font-weight:600">Available</span>
      <span style="background:[LIGHT_BG];color:[ACCENT];padding:4px 10px;border-radius:6px;font-size:11px;font-weight:600">0.3 mi away</span>
    </div>
    <button style="width:100%;margin-top:12px;padding:10px;background:[ACCENT];color:white;border:none;border-radius:8px;font-size:13px;font-weight:600;cursor:pointer">Request to Borrow</button>
  </div>
</div>
```

**For Interaction decisions (user flows, how actions work):**
Build a vertical numbered flow diagram using `.flow-container`, `.flow-step`, `.flow-step-number`, `.flow-step-label`, and `.flow-down-arrow`.

**ALWAYS use this pattern — a vertical column of numbered steps:**
```html
<div class="flow-container">
  <div class="flow-step">
    <span class="flow-step-number">1</span>
    <span class="flow-step-label">Browse books nearby</span>
  </div>
  <div class="flow-down-arrow">↓</div>
  <div class="flow-step highlight">
    <span class="flow-step-number">2</span>
    <span class="flow-step-label">Tap "Request to Borrow"</span>
  </div>
  <div class="flow-down-arrow">↓</div>
  <div class="flow-step">
    <span class="flow-step-number">3</span>
    <span class="flow-step-label">Owner gets notified</span>
  </div>
  <div class="flow-down-arrow">↓</div>
  <div class="flow-step highlight">
    <span class="flow-step-number">4</span>
    <span class="flow-step-label">Owner approves request</span>
  </div>
  <div class="flow-down-arrow">↓</div>
  <div class="flow-step">
    <span class="flow-step-number">5</span>
    <span class="flow-step-label">Chat opens — arrange pickup</span>
  </div>
  <div class="flow-down-arrow">↓</div>
  <div class="flow-step highlight">
    <span class="flow-step-number">6</span>
    <span class="flow-step-label">Both confirm handoff — done!</span>
  </div>
</div>
```

**Branching flows (when the path splits based on a condition):**
Use `.flow-branch-label` for the condition and sub-numbered steps (4a, 4b, etc.) for each branch. Use `.flow-step.error` for failure/error steps:
```html
<div class="flow-container">
  <div class="flow-step">
    <span class="flow-step-number">1</span>
    <span class="flow-step-label">Tap "Request to Borrow"</span>
  </div>
  <div class="flow-down-arrow">↓</div>
  <div class="flow-step error">
    <span class="flow-step-number">2</span>
    <span class="flow-step-label">Request fails</span>
  </div>
  <div class="flow-down-arrow">↓</div>
  <div class="flow-step highlight">
    <span class="flow-step-number">3</span>
    <span class="flow-step-label">Button changes to suggested action</span>
  </div>
  <div class="flow-branch-label">if book was taken ↓</div>
  <div class="flow-step">
    <span class="flow-step-number">3a</span>
    <span class="flow-step-label">Button says "See Similar Books"</span>
  </div>
  <div class="flow-branch-label">if network error ↓</div>
  <div class="flow-step">
    <span class="flow-step-number">3b</span>
    <span class="flow-step-label">Button says "Try Again"</span>
  </div>
</div>
```

**IMPORTANT flow diagram rules:**
- ALWAYS use a vertical single-column layout. NEVER use horizontal rows — they create confusing arrow connections.
- ALWAYS number every step with `.flow-step-number` (1, 2, 3, etc.). Use sub-numbers (4a, 4b) for branches.
- Use `↓` arrows (`.flow-down-arrow`) between sequential steps
- Use `.flow-branch-label` with a condition ("if book was taken ↓") before branching sub-steps
- Use `.flow-step.error` for failure/error steps (red styling)
- Use `.highlight` on the 2-3 most important/differentiating steps (the ones that make this option unique)
- Keep to 4-7 steps. Combine trivial steps if needed.
- In the **option-summary text below the diagram**, reference step numbers: "In step 2, the borrower taps..." This ties the visual to the explanation.
- Each step label should be a short action phrase (verb first): "Tap request button", "Owner approves", "Chat opens"

**For Information Architecture decisions (navigation, content hierarchy):**
Build a mini site-map using the `.sitemap`, `.sitemap-level`, and `.sitemap-node` CSS classes:
```html
<div class="sitemap">
  <div class="sitemap-level">
    <div class="sitemap-node primary">Map View</div>
  </div>
  <div class="sitemap-connector"></div>
  <div class="sitemap-level">
    <div class="sitemap-node">My Books</div>
    <div class="sitemap-node">Browse</div>
    <div class="sitemap-node">Messages</div>
    <div class="sitemap-node">Profile</div>
  </div>
</div>
```

**For Technical decisions (framework, database, architecture):**
Build an architecture diagram using the `.arch-diagram`, `.arch-layer`, and `.arch-box` CSS classes:
```html
<div class="arch-diagram">
  <div class="arch-layer">
    <div class="arch-box frontend">React SPA</div>
  </div>
  <div class="arch-arrow">↕</div>
  <div class="arch-layer">
    <div class="arch-box backend">REST API</div>
    <div class="arch-box service">Auth</div>
  </div>
  <div class="arch-arrow">↕</div>
  <div class="arch-layer">
    <div class="arch-box database">PostgreSQL</div>
  </div>
</div>
```

For some technical decisions (like "which database"), a simple stats/features list may be more useful than a diagram — use your judgment. The point is to help the user *see* the difference, not to force a visual where one doesn't help.

### Comparison Table Dimensions

Choose 5-8 dimensions that genuinely matter for the specific decision. Adapt per category:

**Technical decisions:**
- Learning curve, Community/ecosystem, Performance, Scalability, Cost, Hosting options, Best suited for

**Visual/UX decisions:**
- Mood/feeling, Accessibility, Brand alignment, User demographic fit, Trend durability, Implementation effort

**Interaction decisions:**
- Number of steps, User effort, Error recovery, Speed to complete, Flexibility, Familiarity

**IA decisions:**
- Discoverability, Scalability (as content grows), Mobile friendliness, Cognitive load, Engagement pattern

---

## LANDING PAGE TEMPLATE

The landing page at `.decisions/index.html` shows all decisions at a glance:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Decision Hub — [Project Name]</title>
  <style>
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

    body {
      font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', system-ui, sans-serif;
      background: #f1f5f9;
      color: #1a1a2e;
      min-height: 100vh;
      padding: 2rem;
      line-height: 1.6;
    }

    .container {
      max-width: 900px;
      margin: 0 auto;
    }

    header {
      text-align: center;
      margin-bottom: 2rem;
      padding-bottom: 1.5rem;
      border-bottom: 2px solid #e2e8f0;
    }

    header h1 {
      font-size: 1.6rem;
      font-weight: 700;
      color: #0f172a;
      letter-spacing: -0.02em;
    }

    .project-description {
      font-size: 1rem;
      color: #475569;
      margin-top: 0.5rem;
    }

    /* Progress bar */
    .progress-section {
      margin: 1.5rem 0;
    }

    .progress-label {
      font-size: 0.85rem;
      color: #64748b;
      margin-bottom: 0.5rem;
      display: flex;
      justify-content: space-between;
    }

    .progress-bar {
      width: 100%;
      height: 8px;
      background: #e2e8f0;
      border-radius: 999px;
      overflow: hidden;
    }

    .progress-fill {
      height: 100%;
      background: #059669;
      border-radius: 999px;
      transition: width 0.3s ease;
    }

    /* Decision list */
    .decision-list {
      display: flex;
      flex-direction: column;
      gap: 1rem;
    }

    .decision-item {
      background: white;
      border-radius: 12px;
      padding: 1.25rem 1.5rem;
      box-shadow: 0 1px 3px rgba(0,0,0,0.06);
      border: 2px solid #e2e8f0;
      text-decoration: none;
      color: inherit;
      display: flex;
      align-items: center;
      gap: 1rem;
      transition: border-color 0.2s;
    }

    .decision-item:hover {
      border-color: #6366f1;
    }

    .decision-item.resolved {
      border-left: 4px solid #059669;
    }

    .decision-item.pending {
      border-left: 4px solid #f59e0b;
    }

    .decision-number-badge {
      width: 36px;
      height: 36px;
      border-radius: 999px;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 0.8rem;
      font-weight: 700;
      flex-shrink: 0;
    }

    .decision-item.resolved .decision-number-badge {
      background: #ecfdf5;
      color: #059669;
    }

    .decision-item.pending .decision-number-badge {
      background: #fffbeb;
      color: #d97706;
    }

    .decision-info {
      flex: 1;
    }

    .decision-info h3 {
      font-size: 1rem;
      font-weight: 600;
      color: #0f172a;
    }

    .decision-meta {
      display: flex;
      align-items: center;
      gap: 0.5rem;
      margin-top: 0.3rem;
      flex-wrap: wrap;
    }

    .landing-category-badge {
      display: inline-block;
      padding: 2px 8px;
      border-radius: 999px;
      font-size: 0.6rem;
      font-weight: 700;
      letter-spacing: 0.05em;
      text-transform: uppercase;
    }

    .landing-category-badge.technical { background: #ede9fe; color: #6d28d9; }
    .landing-category-badge.visual { background: #fce7f3; color: #be185d; }
    .landing-category-badge.interaction { background: #e0f2fe; color: #0369a1; }
    .landing-category-badge.ia { background: #ecfdf5; color: #047857; }

    .decision-status {
      font-size: 0.8rem;
      color: #64748b;
    }

    .decision-status .chosen-text {
      color: #059669;
      font-weight: 600;
    }

    .decision-status .pending-text {
      color: #d97706;
      font-weight: 600;
    }

    .decision-summary {
      font-size: 0.8rem;
      color: #64748b;
      margin-top: 0.25rem;
    }

    .arrow-icon {
      color: #94a3b8;
      font-size: 1.1rem;
      flex-shrink: 0;
    }

    .footer-note {
      text-align: center;
      margin-top: 2rem;
      font-size: 0.8rem;
      color: #94a3b8;
    }
  </style>
</head>
<body>
  <div class="container">
    <header>
      <h1>Decision Hub</h1>
      <p class="project-description">[Project Name] — [1-sentence description]</p>
    </header>

    <div class="progress-section">
      <div class="progress-label">
        <span>[X] of [Y] decisions made</span>
        <span>[percentage]%</span>
      </div>
      <div class="progress-bar">
        <div class="progress-fill" style="width: [percentage]%"></div>
      </div>
    </div>

    <div class="decision-list">
      <!-- For each decision: -->
      <a href="decision-001-slug.html" class="decision-item [resolved|pending]">
        <div class="decision-number-badge">1</div>
        <div class="decision-info">
          <h3>[Decision Title]</h3>
          <div class="decision-meta">
            <span class="landing-category-badge [technical|visual|interaction|ia]">[Category]</span>
            <span class="decision-status">
              <!-- If resolved: -->
              <span class="chosen-text">Chosen: Option B — [Title]</span>
              <!-- If pending: -->
              <span class="pending-text">Pending</span>
            </span>
          </div>
          <p class="decision-summary">[One sentence summary]</p>
        </div>
        <span class="arrow-icon">→</span>
      </a>
      <!-- ... repeat for each decision ... -->
    </div>

    <p class="footer-note">
      To change a decision, tell Claude: "for decision-001 I want Option B instead"
    </p>
  </div>
</body>
</html>
```

---

## EDGE CASES

**User skips a decision:** "Skip this one" or "doesn't matter" → Set status to "chosen" with `chosenOption: "skip"`, `chosenTitle: "Skipped — Claude will decide"`. Use your recommendation when implementing.

**User gives a custom answer not matching any option:** "I want to use Postgres with Prisma ORM" → Record it as a custom choice. Set `chosenOption: "custom"`, `chosenTitle: "[their description]"`. Update the HTML with a new card labeled "CUSTOM CHOICE" that reflects what they said.

**User wants to revisit the decision list:** "What decisions have we made?" or "show me the overview" → Open the landing page: `open .decisions/index.html`

**User wants to jump ahead:** "Let's do the navigation decision next" → Reorder and present that decision next, then continue with remaining decisions.

**Existing .decisions directory:** If `.decisions/` already exists from a prior session, read `decisions.json` to understand what's been decided. Resume from the first pending decision. Tell the user: "I see we've already made N decisions. Picking up where we left off with Decision M: [Title]."

**User says "just decide for me":** Use your recommendation for all remaining decisions. Record them all, generate the implementation plan, and present it.

---

## IMPORTANT REMINDERS

1. **Always 4 options.** Not 3, not 5. Exactly 4. Unless the user has asked for more.
2. **Always include a recommendation.** Mark it with the amber badge. Explain WHY you recommend it in the option summary.
3. **Plain English everywhere.** If you use a technical term, explain it in the same sentence. "Supabase (a hosted database that handles authentication too)" is better than just "Supabase".
4. **The comparison table is mandatory.** Every decision page must have one below the cards. Pick dimensions that actually help differentiate the options.
5. **Visual previews should render actual UI.** For visual/UX decisions, build real HTML/CSS components. For interaction decisions, build flow diagrams. For IA decisions, build sitemaps. For technical decisions, build architecture diagrams or show code samples.
6. **Open the HTML automatically.** Always run `open .decisions/decision-NNN-slug.html` after generating.
7. **Update the landing page after every change.** The landing page should always reflect the current state.
8. **Self-contained HTML.** No external dependencies except CDN-hosted fonts or Chart.js when needed. Everything should work by opening the file directly in a browser.
9. **Wait for the user.** After presenting a decision, STOP and wait. Do not proceed to the next decision until the user has made a choice.
10. **Handle decision changes gracefully.** When a user changes a past decision, update both the individual HTML and the landing page. Flag any downstream decisions that might be affected.
