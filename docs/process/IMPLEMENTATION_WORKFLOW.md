# Implementation Workflow

**Purpose:** Standard flow from spec to code to PR for the Lunar Reminder App.

---

## Source of Truth

| Content | Canonical Location |
|---------|-------------------|
| Implementation specs (features, API, data model) | `openspec/specs/capabilities/` |
| Reference docs (architecture, guides, ADRs, glossary) | `docs/` subdirectories |
| OpenSpec change tracking | `openspec/changes/` |
| Legacy monolith documents (read-only) | `docs/_archive/` |

---

## 5-Phase Workflow

### Phase 1: Identify

Understand what needs to be built. Check existing specs and docs.

**Actions:**
1. Read the relevant capability spec in `openspec/specs/capabilities/`
2. Review referenced docs (architecture, data model, API)
3. Use `/opsx:explore` to investigate open questions or clarify requirements

**Output:** Clear understanding of scope and approach.

---

### Phase 2: OpenSpec Change

Create a tracked change using the OpenSpec workflow.

**Actions:**
1. `/opsx:new` — Start a new change (creates proposal)
2. `/opsx:continue` — Generate next artifact (spec → tasks → design)
3. `/opsx:ff` — Or fast-forward to generate all artifacts at once

**Artifacts generated (in order):**
1. **Proposal** (~500 words) — What, why, non-goals
2. **Spec** — Detailed technical specification
3. **Tasks** — Implementation tasks (< 300 LOC each, < 2 hours)
4. **Design** — UI/UX details (NativeWind classes, accessibility)

**Output:** Complete change with all artifacts in `openspec/changes/[change-name]/`.

---

### Phase 3: Implement

Code the change following the task breakdown.

**Actions:**
1. `/opsx:apply` — Implement tasks from the change
2. Follow CLAUDE.md workflow (branch → code → test → lint → commit)
3. Each task = 1 commit, < 300 LOC
4. Run tests before each commit

**Branch naming:**
```
feature/[jira-id]-[short-description]
bugfix/[jira-id]-[short-description]
refactor/[jira-id]-[short-description]
```

**Commit format:** Conventional Commits with scope
```
feat(events): add lunar date picker component
fix(notifications): correct timezone offset for VN
test(auth): add login flow integration tests
```

**Output:** Working implementation on feature branch with tests passing.

---

### Phase 4: PR & Review

Create PR and get human review.

**Actions:**
1. Push branch: `git push origin HEAD`
2. Create PR via `gh pr create` with:
   - Title: `[JIRA-123] Short description`
   - Description: What / Why / How / Tests / Screenshots
3. `/opsx:verify` — Verify implementation matches change artifacts
4. Wait for human review → address feedback → merge

**PR Description Template:**
```markdown
## What
[Description of changes]

## Why
[Link to Jira ticket / business reason]

## How
[Key technical changes]

## Tests
- [ ] Unit tests passing
- [ ] Integration tests passing
- [ ] Manual QA verified

## OpenSpec Change
Reference: `openspec/changes/[change-name]/`
```

**Output:** Merged PR on main branch.

---

### Phase 5: Archive

Sync specs and archive the completed change.

**Actions:**
1. `/opsx:sync` — Sync delta specs from change to main capability specs
2. `/opsx:archive` — Archive the completed change
3. Update `.claude/current-state.md` with completion status
4. Notify: "Task hoàn thành. PR link: [link]. Ready for QA/deploy."

**Output:** Clean state, specs updated, change archived.

---

## Quick Command Reference

| Command | When to Use |
|---------|-------------|
| `/opsx:explore` | Investigate, brainstorm, clarify before starting |
| `/opsx:new` | Start a new change (creates proposal) |
| `/opsx:continue` | Generate next artifact in sequence |
| `/opsx:ff` | Fast-forward: generate all artifacts at once |
| `/opsx:apply` | Implement tasks from a change |
| `/opsx:verify` | Check implementation matches spec before PR |
| `/opsx:sync` | Sync change specs to main capability specs |
| `/opsx:archive` | Archive completed change |
| `/opsx:bulk-archive` | Archive multiple completed changes |
| `/opsx:onboard` | Walk through a complete workflow cycle (learning) |

---

## Rules

- **Always start with Phase 1** — Never code without reading the spec first
- **Tasks < 300 LOC** — Break large changes into smaller tasks
- **Test before commit** — Unit + integration tests must pass
- **Human review required** — Never merge without PR approval
- **Archive after merge** — Keep openspec/changes/ clean

---

## Related Documents

- [CLAUDE.md](../../CLAUDE.md) — Full coding standards and workflow rules
- [Project Lifecycle Checklist](./PROJECT_LIFECYCLE_CHECKLIST.md) — End-to-end project checklist
- [MR Checklist](../guides/MR_CHECKLIST.md) — Pull request review checklist
- [OpenSpec Config](../../openspec/config.yaml) — Project context for spec generation
- [Capability Specs](../../openspec/specs/capabilities/) — Implementation source of truth

---

**Last Updated:** 2026-02-05
