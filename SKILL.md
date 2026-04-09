---
name: better-plan-mode
description: "Planning mode that surfaces a single design decision as a rich HTML document with options, a comparison table, and a recommendation."
argument-hint: "[describe what you want to build]"
---

# Better Plan Mode

You are helping the user think through one design decision for what they're building. Each invocation surfaces a single decision, presents it as a rich HTML page, and hands it back to the user.

The user's request is: **$ARGUMENTS**

**Core principles:**
- Write in plain English. Talk like a smart friend.
- Present exactly 4 options.
- Always include a recommendation and explain why.
- Each invocation is self-contained and starts fresh.

---

## PHASE 1 — Understand the Request

Read `$ARGUMENTS`. If it's empty or too vague, ask one quick question:

> "Tell me a bit more about what you're building so I can pick a decision worth thinking about."

Otherwise, proceed.

---

## PHASE 2 — Pick a Decision

Pick a design decision that feels relevant to what the user described. Vary the category each time so the surfaced decision feels fresh:

- **Technical** — framework, database, hosting, auth
- **Visual** — overall look and feel, color palette, typography
- **Interaction** — a key user flow, navigation pattern, onboarding
- **Information Architecture** — what's in the nav, page structure

Tell the user briefly:

> "Here's a decision worth thinking about for this project: **[decision title]**. Opening it in your browser now."

---

## PHASE 3 — Generate the Decision HTML

Write a self-contained HTML file to a temp location (e.g. `/tmp/decision-[slug].html`) following the structure in the **HTML TEMPLATE REFERENCE** below. It should contain:

- A header with the decision title and a one-paragraph plain-English description
- Four option cards (A, B, C, D), each with a name, a short summary, pros, and cons
- One option marked as "Recommended"
- A small comparison table

Then open it:

```bash
open /tmp/decision-[slug].html
```

---

## PHASE 4 — Hand It Back

> "Take a look at the four options and let me know which one feels right. Run the skill again whenever you want to think through another decision."

The skill ends here.

---

## HTML TEMPLATE REFERENCE

The HTML file should be a single self-contained page with inline CSS. Structure:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>[Decision Title]</title>
  <style>
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
    body {
      font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', system-ui, sans-serif;
      background: #f1f5f9;
      color: #1a1a2e;
      padding: 2rem;
      line-height: 1.6;
    }
    header { text-align: center; margin-bottom: 2rem; max-width: 1000px; margin-left: auto; margin-right: auto; }
    header h1 { font-size: 1.6rem; font-weight: 700; color: #0f172a; }
    .description { font-size: 1rem; color: #475569; margin-top: 0.75rem; max-width: 700px; margin-left: auto; margin-right: auto; }

    .options-grid {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 1.5rem;
      max-width: 1000px;
      margin: 0 auto 2rem;
    }
    .option-card {
      background: white;
      border-radius: 12px;
      border: 2px solid #e2e8f0;
      padding: 1.25rem;
      box-shadow: 0 1px 3px rgba(0,0,0,0.08);
    }
    .option-label { font-size: 0.7rem; font-weight: 800; letter-spacing: 0.1em; text-transform: uppercase; color: #64748b; }
    .option-title { font-size: 1.05rem; font-weight: 600; color: #0f172a; margin-top: 0.25rem; }
    .recommended-badge {
      display: inline-block;
      background: #f59e0b;
      color: white;
      padding: 3px 10px;
      border-radius: 999px;
      font-size: 0.65rem;
      font-weight: 700;
      text-transform: uppercase;
      margin-left: 0.5rem;
    }
    .summary { font-size: 0.875rem; color: #334155; margin: 0.75rem 0; }
    .pros, .cons { font-size: 0.8rem; margin-top: 0.5rem; }
    .pros strong { color: #059669; }
    .cons strong { color: #e11d48; }

    .comparison-table {
      width: 100%;
      max-width: 1000px;
      margin: 0 auto;
      border-collapse: collapse;
      background: white;
      font-size: 0.85rem;
      border-radius: 12px;
      overflow: hidden;
      box-shadow: 0 1px 3px rgba(0,0,0,0.08);
    }
    .comparison-table th, .comparison-table td {
      padding: 0.75rem 1rem;
      text-align: left;
      border-bottom: 1px solid #f1f5f9;
    }
    .comparison-table th { background: #f8fafc; font-weight: 700; font-size: 0.75rem; text-transform: uppercase; color: #64748b; }

    @media (max-width: 768px) { .options-grid { grid-template-columns: 1fr; } body { padding: 1rem; } }
  </style>
</head>
<body>
  <header>
    <h1>[Decision Title]</h1>
    <p class="description">[2-4 sentences in plain English explaining what this decision is about and why it matters.]</p>
  </header>

  <div class="options-grid">
    <div class="option-card">
      <span class="option-label">Option A</span>
      <div class="option-title">[Option A name]</div>
      <p class="summary">[Plain English summary of what this option is.]</p>
      <p class="pros"><strong>Pros:</strong> [comma-separated]</p>
      <p class="cons"><strong>Cons:</strong> [comma-separated]</p>
    </div>
    <div class="option-card">
      <span class="option-label">Option B</span>
      <div class="option-title">[Option B name] <span class="recommended-badge">Recommended</span></div>
      <p class="summary">[Plain English summary.]</p>
      <p class="pros"><strong>Pros:</strong> [...]</p>
      <p class="cons"><strong>Cons:</strong> [...]</p>
    </div>
    <div class="option-card">
      <span class="option-label">Option C</span>
      <div class="option-title">[Option C name]</div>
      <p class="summary">[...]</p>
      <p class="pros"><strong>Pros:</strong> [...]</p>
      <p class="cons"><strong>Cons:</strong> [...]</p>
    </div>
    <div class="option-card">
      <span class="option-label">Option D</span>
      <div class="option-title">[Option D name]</div>
      <p class="summary">[...]</p>
      <p class="pros"><strong>Pros:</strong> [...]</p>
      <p class="cons"><strong>Cons:</strong> [...]</p>
    </div>
  </div>

  <table class="comparison-table">
    <thead>
      <tr><th></th><th>A</th><th>B</th><th>C</th><th>D</th></tr>
    </thead>
    <tbody>
      <tr><td>[Dimension 1]</td><td>...</td><td>...</td><td>...</td><td>...</td></tr>
      <tr><td>[Dimension 2]</td><td>...</td><td>...</td><td>...</td><td>...</td></tr>
      <tr><td>[Dimension 3]</td><td>...</td><td>...</td><td>...</td><td>...</td></tr>
    </tbody>
  </table>
</body>
</html>
```
