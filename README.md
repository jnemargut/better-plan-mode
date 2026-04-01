# Better Plan Mode

An AI coding skill that transforms project planning into a visual, interactive decision-making experience. Instead of quick yes/no prompts, you get rich HTML documents with side-by-side options, visual previews, comparison tables, and a persistent decision history.

![Better Plan Mode in action](demo.gif)

## What It Does

When you're starting a new project or feature, Better Plan Mode walks you through every meaningful decision, one at a time, with:

- **4 options per decision** with a clear recommendation and plain English explanations
- **Visual previews** -- rendered UI mockups for design decisions, flow diagrams for interaction decisions, sitemaps for navigation decisions, architecture diagrams for technical decisions
- **Comparison tables** showing how options stack up across relevant dimensions
- **A decision history** saved as HTML files you can revisit anytime
- **A landing page** showing all decisions at a glance with status tracking

### Decision Categories

| Category | What It Covers | Visual Preview |
|----------|---------------|----------------|
| **Technical** | Tech stack, database, auth, hosting, API design | Architecture diagrams, code samples |
| **Visual/UX** | Style direction, color, typography, component design | Rendered HTML/CSS mockups |
| **Interaction** | User flows, onboarding, how key actions work | Step-by-step flow diagrams |
| **Information Architecture** | Navigation, content hierarchy, page structure | Mini sitemaps, nav visualizations |

## Install

The skill is a single `SKILL.md` file. Drop it into the right directory for your AI coding tool.

**Claude Code:**
```bash
git clone https://github.com/jnemargut/better-plan-mode ~/.claude/skills/better-plan-mode
```

**Codex:**
```bash
git clone https://github.com/jnemargut/better-plan-mode ~/.codex/skills/better-plan-mode
```

**Gemini CLI:**
```bash
git clone https://github.com/jnemargut/better-plan-mode ~/.gemini/skills/better-plan-mode
```

**Cursor:**

Cursor uses `.mdc` rule files instead of a skills directory. Copy the skill content into a rule:
```bash
mkdir -p .cursor/rules
cp SKILL.md .cursor/rules/better-plan-mode.mdc
```
Then open `.cursor/rules/better-plan-mode.mdc` and replace the frontmatter with:
```yaml
---
description: "Enhanced planning mode that presents decision points as rich HTML documents with visual previews, comparison tables, and recommendations."
alwaysApply: false
---
```

The skill should work with any AI coding tool that supports custom instructions or skill files. If yours uses a different directory, just put `SKILL.md` wherever your tool looks for skills or prompts.

## Usage

Invoke the skill with:

```
/better-plan-mode I want to build a neighborhood book-sharing app where people can list books, browse nearby, and request to borrow
```

Or any project description:

```
/better-plan-mode A dashboard for tracking my fitness goals with charts and social features
```

### During Planning

Each decision opens as an HTML page in your browser. You can respond with:

- **`Option B`** -- pick that option
- **`Option A but with a darker color palette`** -- customize an option
- **`more options`** -- adds 4 more options to the page
- **`for decision-001 I want Option C instead`** -- change a past decision

### Output

All decisions are saved to a `.decisions/` folder in your project:

```
.decisions/
  index.html                          # Landing page -- overview of all decisions
  decisions.json                      # Machine-readable state
  decision-001-frontend-framework.html
  decision-002-backend-data.html
  decision-003-visual-direction.html
  ...
  implementation-plan.md              # Final plan with task list
```

Open `.decisions/index.html` anytime to see the big picture.

### After Planning

Once all decisions are made, the skill generates an implementation plan with a task list and asks how you want to proceed:

- **Auto mode** -- works through the tasks automatically
- **Step by step** -- asks for approval before each action
- **Let me review** -- you look over the plan first
- **Just the plan** -- you'll implement it yourself

## Better Plan Mode vs. Traditional Plan Mode

| | Traditional Plan Mode | Better Plan Mode |
|---|---|---|
| **Decision format** | 1-2 sentence text prompts | Rich HTML pages with visuals, pros/cons, comparison tables |
| **Options shown** | Usually 2-3 | Always 4 (more on request) |
| **Visual decisions** | Text descriptions only | Rendered mockups, flow diagrams, sitemaps |
| **Recommendation** | Sometimes | Always, with explanation |
| **Decision history** | Lost after session | Saved as browsable HTML files |
| **Changing your mind** | Start over | Just say which decision to update |
| **Token usage** | Lower | Higher (generating HTML + visual previews) |
| **Speed** | Faster | Slower (but more thorough) |
| **Best for** | Quick prototypes, small features | New projects, major features, design-sensitive work |

Better Plan Mode uses more tokens and takes longer because it's generating rich HTML documents with visual previews. The tradeoff is that you make more informed decisions, especially for UX and design choices that are hard to evaluate from text alone.

## Using with Deep Plan Mode

[Deep Plan Mode](https://github.com/jnemargut/deep-plan-mode) is the companion skill that handles the "what and why" — problem validation, target users, market research, business model, and success metrics.

The two skills work as a pipeline:
1. Run `/deep-plan-mode` first to nail the strategy
2. Run `/better-plan-mode` second to plan the implementation

Better Plan Mode automatically detects Deep Plan Mode's output. If it finds a `.decisions/strategy-brief.md` in your project, it reads the strategy decisions and uses them to inform every technical and design recommendation.

## License

MIT
