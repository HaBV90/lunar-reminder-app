# Groups Capability Specification

## Overview

Event grouping and organization capability for categorizing events.

---

## Features

### FR-GROUP-001: Create Group
- **Priority:** Medium
- **Fields:**
  - Name (required, max 50 chars)
  - Color (from palette)
  - Icon (from library)
- **Examples:**
  - "Gia đình nội"
  - "Gia đình ngoại"
  - "Bạn bè"
  - "Công việc"

### FR-GROUP-002: Assign Events to Groups
- One event can belong to multiple groups
- Multi-select when creating event
- Add/remove groups after creation

### FR-GROUP-003: View Events by Group
- "Groups" tab shows all groups
- Tap group → see events in group
- Event colors can follow group color

### FR-GROUP-004: Edit/Delete Group
- Edit name, color, icon
- Delete group confirmation
- Deleting group does NOT delete events
- Events remain, just ungrouped

---

## API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/groups` | List user's groups |
| POST | `/groups` | Create group |
| PUT | `/groups/:id` | Update group |
| DELETE | `/groups/:id` | Delete group |

---

## Data Model

### groups Table
- `id` UUID (PK)
- `user_id` UUID (FK)
- `name` VARCHAR(100)
- `description` TEXT
- `color` VARCHAR(20)
- `icon` VARCHAR(50)
- `sort_order` INTEGER

### event_groups Table (Junction)
- `id` UUID (PK)
- `event_id` UUID (FK)
- `group_id` UUID (FK)
- UNIQUE(event_id, group_id)

See [groups table](../../../docs/data-model/tables/groups.md) for full schema.

---

## Color Palette

Groups can use colors from the app's palette:
- Traditional red: #D32F2F
- Gold: #FBC02D
- Navy: #283593
- Purple: #5E35B1
- Green: #388E3C
- Orange: #FF5722
- Blue: #2196F3
- Pink: #E91E63

---

## Icons

Available icons for groups:
- `family` - Family/household
- `friends` - Social connections
- `work` - Professional
- `temple` - Religious/spiritual
- `heart` - Personal
- `star` - Important
- `calendar` - Generic events

---

## User Stories

**US-GROUP-1:** As a user, I want to create groups so I can organize events by family side.

**US-GROUP-2:** As a user, I want to filter by group so I see only relevant events.

**US-GROUP-3:** As a user, I want to share a group so my family has the same events.

---

## Reference Documentation

- [Architecture Overview](../../../docs/architecture/index.md)
- [Groups Table (full schema)](../../../docs/data-model/tables/groups.md)
- [UI/UX Design System](../../../docs/ui-ux/index.md)

---

## Related Specs

- [Events Capability](../events/spec.md)
- [Notifications Capability](../notifications/spec.md)
