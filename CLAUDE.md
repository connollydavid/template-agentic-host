# CLAUDE.md

Behavioral guidelines to reduce common LLM coding mistakes. Merge with project-specific instructions as needed.

**Tradeoff:** These guidelines bias toward caution over speed. For trivial tasks, use judgment.

## 1. Think Before Coding

**Don't assume. Don't hide confusion. Surface tradeoffs.**

Before implementing:
- State your assumptions explicitly. If uncertain, ask.
- If multiple interpretations exist, present them - don't pick silently.
- If a simpler approach exists, say so. Push back when warranted.
- If something is unclear, stop. Name what's confusing. Ask.

## 2. Simplicity First

**Minimum code that solves the problem. Nothing speculative.**

- No features beyond what was asked.
- No abstractions for single-use code.
- No "flexibility" or "configurability" that wasn't requested.
- No error handling for impossible scenarios.
- If you write 200 lines and it could be 50, rewrite it.

Ask yourself: "Would a senior engineer say this is overcomplicated?" If yes, simplify.

## 3. Surgical Changes

**Touch only what you must. Clean up only your own mess.**

When editing existing code:
- Don't "improve" adjacent code, comments, or formatting.
- Don't refactor things that aren't broken.
- Match existing style, even if you'd do it differently.
- If you notice unrelated dead code, mention it - don't delete it.

When your changes create orphans:
- Remove imports/variables/functions that YOUR changes made unused.
- Don't remove pre-existing dead code unless asked.

The test: Every changed line should trace directly to the user's request.

## 4. Goal-Driven Execution

**Define success criteria. Loop until verified.**

Transform tasks into verifiable goals:
- "Add validation" → "Write tests for invalid inputs, then make them pass"
- "Fix the bug" → "Write a test that reproduces it, then make it pass"
- "Refactor X" → "Ensure tests pass before and after"

For multi-step tasks, state a brief plan:
```
1. [Step] → verify: [check]
2. [Step] → verify: [check]
3. [Step] → verify: [check]
```

Strong success criteria let you loop independently. Weak criteria ("make it work") require constant clarification.

## 5. Audited PLAN.md and PHASEx.md

All changes to PLAN.md and PHASEx.md files MUST be committed and pushed immediately.

Rules:
- Every edit to PLAN.md or any PHASEx.md file (e.g. PHASE1.md, PHASE2.md) triggers a git commit and git push. Do not batch these with other changes.
- After completing a plan step in code, update the relevant plan file to reflect what was actually implemented, then commit and push that update as a separate commit.
- PLAN.md and PHASEx.md files live in the top-level repository only. Never place plan files inside git submodules. Submodules contain the working codebase; planning documents are kept outside of them.

## 6. Automatic Static Site Builds for Self-Documenting Work

All markdown documentation in the repository is automatically built into a static website using mdBook and published to GitHub Pages. This creates a living, browsable record of the project.

Rules:
- A GitHub Actions workflow triggers on every push to the main branch. It builds all .md files (including PLAN.md, PHASEx.md, and any other documentation) into a static HTML site using mdBook.
- The mdBook configuration file (book.toml) and the SUMMARY.md file MUST be committed to the repo. SUMMARY.md defines the sidebar navigation and must be updated whenever a new document is added. book.toml lives in the repository root.
- The GitHub Actions workflow installs mdBook, runs `mdbook build`, and publishes the output directory to the gh-pages branch. GitHub Pages serves this branch automatically. Do not commit built HTML artifacts to the main branch.
- The published site is the single source of truth for project status. Anyone with access to the repository can read current plans, completed phases, and design decisions by visiting the GitHub Pages URL — no local checkout required.
- When a new PHASEx.md file is created or a new document is added, add an entry to SUMMARY.md in the same commit. If SUMMARY.md is not updated, the new document will not appear in the site navigation.

Style:
- The site must be clean, beautiful, and minimalist. Use generous whitespace and avoid clutter, decorative elements, and unnecessary UI chrome. The content is the interface.
- In book.toml, set `default-theme = "light"` and `preferred-dark-theme = "navy"`. Add a custom CSS file (committed to the repo) that includes a `@media (prefers-color-scheme: dark)` block to automatically switch to the dark theme on page load. This way the site respects the reader's OS-level light/dark setting without manual toggling.
- Keep all CSS customisations under 50 lines. Limit changes to subtle refinements — tighter max-width, improved typography, muted colours. Do not override mdBook's built-in themes beyond this.

---

**These guidelines are working if:** fewer unnecessary changes in diffs, fewer rewrites due to overcomplication, and clarifying questions come before implementation rather than after mistakes.
