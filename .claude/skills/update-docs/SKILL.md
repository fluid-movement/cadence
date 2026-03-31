---
name: update-docs
description: Update the docs/ folder after implementing or modifying a feature. Use this after writing or changing code that implements a feature to keep the documentation in sync with the actual implementation.
---

# Update Docs

A feature has just been implemented or modified. Your job is to update `docs/` so it accurately reflects the current state of the codebase — not the plan, the reality.

## What to update

**1. Find the relevant feature doc**

The feature docs live in `docs/features/`. Each file corresponds to a feature area. Identify which file(s) are affected by the change. If $ARGUMENTS is provided, use it as a hint for which feature was changed.

**2. Update the feature doc**

Read the current doc and compare it against what was actually implemented. Update it to reflect reality:
- Remove or correct anything that was planned differently but implemented differently
- Add details that emerged during implementation (edge cases, decisions made, constraints discovered)
- Update field names, behavior descriptions, or configuration options if they changed
- Do NOT remove V2 sections or TODOs — only update what was actually built

**3. Check docs/roadmap.md**

If the feature was V1 and is now implemented, confirm it's listed correctly. If scope changed (something moved from V1 to V2 or vice versa), update it.

**4. Check docs/questions.md**

If any open questions were resolved during implementation, mark them as resolved with `[x]` and add the decision below the question. If new questions emerged, add them.

**5. Check docs/main.md**

If a new feature file was created, add it to the documentation index.

## Rules

- **Reflect reality, not intent.** If something was planned one way but implemented another, the docs follow the implementation.
- **Be specific.** Vague docs are useless. If a field was added with a specific name or behavior, document it exactly.
- **Preserve planning decisions.** Don't remove rationale, V2 sections, or architectural notes — only update what actually changed.
- **Don't over-document.** Don't add implementation details like class names or file paths unless they're relevant to understanding the feature behavior.
- **Flag drift.** If you notice the implementation diverged significantly from the spec in a way that wasn't captured as a decision, add a note so it can be discussed.

## Output

After updating the docs, briefly summarize:
- Which files were updated and why
- Any significant drift between the original spec and what was implemented
- Any new open questions that should be added to questions.md
