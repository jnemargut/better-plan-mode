# Better Plan Mode

A Claude Code skill that transforms project planning into a visual, interactive decision-making experience. Instead of quick yes/no prompts, you get rich HTML documents with side-by-side options, visual previews, comparison tables, and a persistent decision history.

![Better Plan Mode in action](demo.gif)

## What It Does

When you're starting a new project or feature, Better Plan Mode walks you through every meaningful decision — one at a time — with:

- **4 options per decision** with a clear recommendation and plain English explanations
- **Visual previews** — rendered UI mockups for design decisions, flow diagrams for interaction decisions, sitemaps for navigation decisions, architecture diagrams for technical decisions
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

Copy the skill folder to your Claude Code skills directory:

```bash
mkdir -p ~/.claude/skills/better-plan-mode
cp SKILL.md ~/.claude/skills/better-plan-mode/SKILL.md
```

That's it. The skill is immediately available in Claude Code.

## Usage

Invoke the skill in Claude Code with:

```
/better-plan-mode I want to build a neighborhood book-sharing app where people can list books, browse nearby, and request to borrow
```

Or any project description:

```
/better-plan-mode A dashboard for tracking my fitness goals with charts and social features
```

### During Planning

Each decision opens as an HTML page in your browser. You can respond with:

- **`Option B`** — pick that option
- **`Option A but with a darker color palette`** — customize an option
- **`more options`** — adds 4 more options to the page
- **`for decision-001 I want Option C instead`** — change a past decision

### Output

All decisions are saved to a `.decisions/` folder in your project:

```
.decisions/
  index.html                          # Landing page — overview of all decisions
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

- **Auto mode** — Claude works through the tasks automatically
- **Step by step** — Claude asks for approval before each action
- **Let me review** — you look over the plan first
- **Just the plan** — you'll implement it yourself

## Better Plan Mode vs. Traditional Plan Mode

| | Traditional Plan Mode | Better Plan Mode |
|---|---|---|
| **Decision format** | 1-2 sentence text prompts | Rich HTML pages with visuals, pros/cons, comparison tables |
| **Options shown** | Usually 2-3 | Always 4 (more on request) |
| **Visual decisions** | Text descriptions only | Rendered mockups, flow diagrams, sitemaps |
| **Recommendation** | Sometimes | Always, with explanation |
| **Decision history** | Lost after session | Saved as browsable HTML files |
| **Changing your mind** | Start over | Just tell Claude which decision to update |
| **Token usage** | Lower | Higher (generating HTML + visual previews) |
| **Speed** | Faster | Slower (but more thorough) |
| **Best for** | Quick prototypes, small features | New projects, major features, design-sensitive work |

Better Plan Mode uses more tokens and takes longer because it's generating rich HTML documents with visual previews. The tradeoff is that you make more informed decisions, especially for UX and design choices that are hard to evaluate from text alone.

## License

MIT
