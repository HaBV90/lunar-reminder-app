# Data Synchronization Implementation

## Overview

Offline-first architecture with delta sync strategy for seamless data synchronization.

---

## Sync Strategy: Delta Sync with Timestamps

### Principles
1. Each entity has `updated_at` timestamp
2. Client tracks `last_sync_time`
3. On sync: Send client changes + request server changes since `last_sync_time`
4. Conflict resolution: Last Write Wins (server timestamp authoritative)

---

## Sync Flow Diagram

```
Client                              Server
  │ POST /sync                        │
  │ {                                 │
  │   last_sync_time: "...",          │
  │   changes: {                      │
  │     events: { created, updated, deleted } │
  │   }                               │
  │ }                                 │
  ├──────────────────────────────────►│
  │                                   │
  │                   Process changes │
  │                   Find server changes │
  │                   Detect conflicts │
  │                                   │
  │     { server_changes, conflicts,  │
  │       sync_time }                 │
  │◄──────────────────────────────────┤
  │                                   │
Apply changes locally                 │
Update last_sync_time                 │
```

---

## Client-Side Implementation

```typescript
// syncService.ts
class SyncService {
  async sync() {
    const lastSyncTime = await AsyncStorage.getItem('last_sync_time');

    // 1. Get local changes
    const localChanges = await this.getLocalChanges(lastSyncTime);

    // 2. Send to server
    const response = await api.post('/sync', {
      last_sync_time: lastSyncTime,
      changes: localChanges
    });

    // 3. Apply server changes
    await this.applyServerChanges(response.data.server_changes);

    // 4. Handle conflicts
    if (response.data.conflicts.length > 0) {
      await this.resolveConflicts(response.data.conflicts);
    }

    // 5. Update last sync time
    await AsyncStorage.setItem('last_sync_time', response.data.sync_time);
  }

  private async getLocalChanges(since: string) {
    const db = await getDatabase();

    const created = await db.executeSql(
      'SELECT * FROM local_events WHERE sync_status = ? AND created_at > ?',
      ['pending_upload', since]
    );

    const updated = await db.executeSql(
      'SELECT * FROM local_events WHERE sync_status = ? AND updated_at > ?',
      ['pending_upload', since]
    );

    const deleted = await db.executeSql(
      'SELECT server_id FROM local_events WHERE sync_status = ?',
      ['pending_delete']
    );

    return {
      events: {
        created: created.rows.raw(),
        updated: updated.rows.raw(),
        deleted: deleted.rows.raw().map(r => r.server_id)
      }
    };
  }
}
```

---

## Server-Side Implementation

```typescript
// sync.service.ts
@Injectable()
export class SyncService {
  async sync(userId: string, syncDto: SyncDto) {
    // 1. Process client changes
    await this.processClientChanges(userId, syncDto.changes);

    // 2. Get server changes since last sync
    const serverChanges = await this.getServerChanges(
      userId,
      syncDto.last_sync_time
    );

    // 3. Detect conflicts
    const conflicts = await this.detectConflicts(
      syncDto.changes,
      serverChanges
    );

    return {
      server_changes: serverChanges,
      conflicts,
      sync_time: new Date().toISOString()
    };
  }

  private async getServerChanges(userId: string, since: string) {
    const sinceDate = new Date(since);

    const created = await this.eventRepo.find({
      where: {
        userId,
        createdAt: MoreThan(sinceDate)
      }
    });

    const updated = await this.eventRepo.find({
      where: {
        userId,
        updatedAt: MoreThan(sinceDate),
        createdAt: LessThanOrEqual(sinceDate)
      }
    });

    const deleted = await this.eventRepo.find({
      where: {
        userId,
        deletedAt: MoreThan(sinceDate)
      },
      withDeleted: true
    });

    return {
      events: {
        created,
        updated,
        deleted: deleted.map(e => e.id)
      }
    };
  }
}
```

---

## Local Database Schema (SQLite)

```sql
CREATE TABLE local_events (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    server_id TEXT UNIQUE,
    user_id TEXT NOT NULL,
    name TEXT NOT NULL,
    type TEXT NOT NULL,
    calendar_type TEXT NOT NULL,
    lunar_day INTEGER,
    lunar_month INTEGER,
    solar_date TEXT NOT NULL,
    -- ... other fields
    sync_status TEXT DEFAULT 'pending_upload',
    last_modified TEXT NOT NULL,
    created_at TEXT NOT NULL,
    updated_at TEXT NOT NULL
);

CREATE TABLE sync_queue (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    operation TEXT NOT NULL,  -- 'create', 'update', 'delete'
    entity_type TEXT NOT NULL,
    entity_id INTEGER NOT NULL,
    server_id TEXT,
    data TEXT,  -- JSON payload
    timestamp INTEGER NOT NULL,
    retry_count INTEGER DEFAULT 0
);
```

---

## Conflict Resolution

### Strategy: Last Write Wins
- Server timestamp is authoritative
- If client and server both modified same entity, server version wins
- Future: Allow user to manually resolve conflicts

### Conflict Detection
```typescript
private async detectConflicts(clientChanges, serverChanges) {
  const conflicts = [];

  for (const clientUpdate of clientChanges.events.updated) {
    const serverUpdate = serverChanges.events.updated.find(
      s => s.id === clientUpdate.serverId
    );

    if (serverUpdate) {
      conflicts.push({
        entityId: clientUpdate.serverId,
        clientVersion: clientUpdate,
        serverVersion: serverUpdate,
        resolution: 'server_wins'
      });
    }
  }

  return conflicts;
}
```

---

## Sync Triggers

- App launch
- Event created/updated/deleted
- Every 5 minutes (when online)
- Manual pull-to-refresh

---

## Related

- [Settings Capability Spec](../../openspec/specs/capabilities/settings/spec.md)
- [Sync API](../api/sync-endpoints.md)
