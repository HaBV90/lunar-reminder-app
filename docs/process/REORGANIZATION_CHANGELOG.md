# Reorganization Changelog

**Date:** 2026-02-05
**Author:** Ha (PM) + Claude (AI Assistant)
**Scope:** Project structure reorganization — docs, .claude, openspec

---

## Problems Identified

### Problem 1: Triple Documentation Duplication
Content trùng lặp ở 3 nơi:
- Legacy monolith docs: `docs/01_*.md`, `02_*.md`, `04_*.md` (250+ KB)
- Reorganized modular docs: `docs/requirements/`, `docs/architecture/`, etc.
- OpenSpec specs: `openspec/specs/capabilities/`

Ví dụ: Events spec tồn tại ở `docs/requirements/features/events.md`, `openspec/specs/capabilities/events/spec.md`, và trong `01_PRODUCT_REQUIREMENTS.md`.

### Problem 2: .claude/ Folder Chaos
- 10 duplicate pairs: `.claude/skills/openspec-*` ↔ `.claude/commands/opsx/*` (98% identical)
- `commands/backend.md` (727 lines) duplicates `commands/backend/` (6 modular files)
- 4 tiny files (`workflow.md`, `gitflow.md`, `roles.md`, `rules.md`) totaling 50 lines
- Total: 38 files, nhiều thừa

### Problem 3: No Implementation Workflow
- Specs xong → không có documented flow để code
- `openspec/config.yaml` gần như trống
- `openspec/changes/` directory chưa tồn tại

### Problem 4: Legacy + Modular Docs Cùng Tồn Tại
- `docs/03_UI_UX_SPEC.md` byte-identical với `docs/ui-ux/index.md`
- `docs/07_GLOSSARY.md` byte-identical với `docs/reference/glossary.md`
- `docs/index.md` links đến subdirectories nhưng legacy files vẫn ở root

---

## Architecture Decisions

### AD-R01: Source of Truth
| Content | Canonical Location |
|---------|-------------------|
| Implementation specs | `openspec/specs/capabilities/` |
| Reference docs | `docs/` subdirectories |
| Legacy monoliths | `docs/_archive/` (read-only) |

### AD-R02: .claude/ Organization
- Keep `commands/opsx/` as canonical OpenSpec commands
- Remove `skills/` (duplicate)
- Keep `commands/backend/` modular, remove `backend.md` monolith
- Merge 4 tiny config files into `CLAUDE.md`

### AD-R03: OpenSpec as Implementation Driver
- Populate `config.yaml` with full project context
- Create `changes/` directory for workflow
- Flow: spec → `/opsx:new` → artifacts → `/opsx:apply` → code → PR → `/opsx:archive`

---

## Change Manifest

### Files Created
| File | Purpose | Lines |
|------|---------|-------|
| `docs/_archive/README.md` | Explain archive purpose | ~25 |
| `docs/process/IMPLEMENTATION_WORKFLOW.md` | Spec→Code→PR flow | ~150 |
| `docs/process/REORGANIZATION_CHANGELOG.md` | This file | ~150 |
| `openspec/changes/.gitkeep` | Enable workflow directory | 0 |

### Files Moved to Archive
| Source | Destination | Lines |
|--------|-------------|-------|
| `docs/01_PRODUCT_REQUIREMENTS.md` | `docs/_archive/` | 1,159 |
| `docs/02_TECHNICAL_SPEC.md` | `docs/_archive/` | 2,808 |
| `docs/04_DATA_MODEL.md` | `docs/_archive/` | 968 |
| `docs/CHANGE_HISTORY.md` | `docs/_archive/` | 824 |

### Files Deleted
| File | Reason | Lines |
|------|--------|-------|
| `docs/03_UI_UX_SPEC.md` | Byte-identical to `docs/ui-ux/index.md` | 140 |
| `docs/07_GLOSSARY.md` | Byte-identical to `docs/reference/glossary.md` | 332 |
| `.claude/skills/` (10 files) | 98% identical to `commands/opsx/` | 1,934 |
| `.claude/commands/backend.md` | Duplicate of `commands/backend/` modular | 727 |
| `.claude/workflow.md` | Content in root CLAUDE.md | 14 |
| `.claude/gitflow.md` | Content in root CLAUDE.md | 19 |
| `.claude/roles.md` | Merged into `.claude/CLAUDE.md` | 8 |
| `.claude/rules.md` | Content in root CLAUDE.md | 9 |

### Files Updated
| File | Changes |
|------|---------|
| `.claude/CLAUDE.md` | Added Roles section |
| `.claude/current-state.md` | Updated for reorganization |
| `openspec/config.yaml` | Full project context + rules |
| `docs/index.md` | Process section, source-of-truth table, archive note |
| `CLAUDE.md` (root) | Updated glossary path, added workflow ref |
| `README.md` | Updated doc links |
| `docs/guides/FRONTEND_SETUP.md` | Updated references |
| `docs/guides/BACKEND_SETUP.md` | Updated references |
| Various `docs/data-model/*.md` | Updated references |
| Various `docs/changelog/*.md` | Updated references |
| `openspec/specs/capabilities/*.md` | Added cross-references to docs/ |

---

## Results

### Verification (2026-02-05)
- `.claude/` files: **23** (from 38 → 40% reduction)
- `docs/` root: **No numbered files** — only subdirectories + index.md
- `openspec/changes/` directory: **Exists**
- Legacy references: Only in `_archive/`, `REORGANIZATION_CHANGELOG.md`, `changelog/index.md` (appropriate)
- All cross-references updated and verified

### File Counts
| Area | Before | After | Change |
|------|--------|-------|--------|
| `.claude/` | 38 | 23 | -15 files |
| `docs/` root files | 6 numbered | 0 | Clean |
| `openspec/` | 7 | 9 | +2 (config, .gitkeep) |

### Net LOC Change
- Removed: ~3,183 lines (skills, backend.md, duplicates, tiny files)
- Added: ~500 lines (changelog, workflow, archive README, cross-refs)
- Moved to archive: ~5,759 lines (4 legacy monoliths)

---

## Status: COMPLETE

All 6 layers verified and finalized on 2026-02-05.
