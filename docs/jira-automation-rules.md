# Jira Automation Rules — Build Once, Reuse Every Project

These are the rules to set up in **one** Jira project first. Once they work,
export them and import them into every new customer project's board instead
of rebuilding them by hand each time.

## The 5 rules to build

### 1. Branch created → In Progress
- **Trigger:** Branch created
- **Condition:** none needed
- **Action:** Transition issue → In Progress
- *(This is the one you've already set up.)*

### 2. Pull Request opened → In Review
- **Trigger:** Pull request created
- **Condition:** Branch name matches `[A-Z]+-[0-9]+.*` (only fires for
  correctly-named branches — pairs with the BranchNameCheck workflow)
- **Action:** Transition issue → In Review

### 3. Pull Request merged into qa-current or qa-next → In QA / Testing
- **Trigger:** Pull request merged
- **Condition:** Destination branch matches `qa-current` OR `qa-next`
- **Action:** Transition issue → In QA / Testing

### 4. Build failed → Comment on ticket
- **Trigger:** Build status changed
- **Condition:** New build status = Failed
- **Action:** Add comment: "⚠️ The build for this change failed. Check the
  Actions tab in GitHub for details."
- *(Optional: also add a label like `build-failed` so it's easy to filter
  the board to see what's currently broken.)*

### 5. Pull Request merged into main → Done / Released
- **Trigger:** Pull request merged
- **Condition:** Destination branch = `main`
- **Action:** Transition issue → Done, then Add comment: "✅ Released."
  (If the auto-release-on-merge workflow posts the version number
  somewhere accessible, this comment can be upgraded to include it —
  e.g. "✅ Released in v1.4.2.")

## Smart Commits (optional, but useful for quick fixes)

Typing this directly in a commit message updates the ticket without
opening Jira at all:

```
PROJ-123 #comment Fixed the null check on save #time 2h
```

Format: `TICKET-ID #command details`. Common commands: `#comment`,
`#time`, and `#<workflow-status-name>` (e.g. `#done`) to transition
the ticket directly.

## Reusing these rules for every new customer project

1. Build and test all 5 rules above in one real project first.
2. In Jira: **Settings → System → Automation rules**.
3. Hover the rule → **⋯ → Export** (repeat for each, or export all at once
   from the same menu). This downloads a JSON file per rule (or one
   combined file for "export all").
4. When a new customer project is created: **Automation rules → ⋯ →
   Import rules**, select the JSON file(s), and choose the new project
   as the destination.
5. Imported rules land **disabled by default** — you have to manually
   switch each one on after importing. Don't skip this step.

Note: importing rules requires **global Jira administrator** access, not
just project admin — worth checking who on the team has that before
relying on this step being quick.
