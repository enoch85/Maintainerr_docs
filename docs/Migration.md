---
description: Switching between Plex and Jellyfin, rule migration for YAML and Community imports
title: Migration
---

!!! tip
    Backup `/opt/data/maintainerr.db` before major changes.

## Media Server Switching

Switch between Plex and Jellyfin at any time with automatic rule migration.

<details>
<summary><strong>Technical Details</strong></summary>

1. UI calls `GET /settings/media-server/switch/preview/:targetServerType`
2. Server counts data and calls `previewMigration()`
3. UI displays preview and waits for confirmation
4. UI posts to `POST /settings/media-server/switch` with `{ targetServerType, migrateRules }`
5. Server executes switch in transaction:
   - Validate request (check not already on target)
   - Count existing data (for response)
   - **Migrate rules** if requested via `migrateRules()` (before clearing)
   - **Clear data** via `clearMediaServerData()`:
     - If NOT migrating: Clear CollectionMedia → CollectionLog → Exclusion → RuleGroup (cascades to Rules) → Collection
     - If migrating: Clear CollectionMedia → CollectionLog → Exclusion, then UPDATE RuleGroup (reset libraryId) and UPDATE Collection (reset mediaServerId, set new mediaServerType)
   - Update settings (`updateMediaServerType()`)
   - Uninitialize old adapter (PlexApiService or JellyfinAdapterService)
   - Commit transaction (or rollback on error)

**Key behaviors:**
- Foreign key order respected (children before parents)
- When migrating: Collections preserved but media server refs nulled
- libraryId set to empty string (forces user reassignment)
- Old adapter uninitialize prevents stale connections

</details>

### Process

1. **Preview** - See what will be cleared, kept, and migrated
2. **Confirm** - Choose whether to migrate rules or start fresh
3. **Execute** - Applied in a transaction (rollback on error)

**Cleared:**

- Collections and collection media
- Exclusions
- Collection logs
- Rule groups and rules (if not migrating)

**Kept:**

- General settings
- Radarr/Sonarr configurations
- Overseerr/Jellyseerr settings
- Tautulli configuration
- Notification settings

### Rule Migration

When migrating during a switch:

- Compatible rules are automatically converted
- Incompatible rules are skipped (logged)
- Libraries must be re-assigned (IDs differ between servers)
- Collections are preserved (metadata kept, recreated on new server)

### Incompatible Features

**Plex → Jellyfin** (skipped):

- Watchlist integration
- External ratings (IMDb, Rotten Tomatoes, TMDb)
- Smart collections

**Jellyfin → Plex**:

- No restrictions (Plex supports all Jellyfin features)

!!! warning
    Re-assign libraries in each rule group after switching. Collections won't function until set.

## YAML Import Migration

When importing YAML rules, migration is automatic and transparent.

<details>
<summary><strong>Technical Details</strong></summary>

1. UI posts to `/rules/yaml/decode` with YAML content
2. Server decodes YAML to rules
3. Server automatically calls migration before returning
4. UI receives already-migrated rules

</details>

Compatible rules convert automatically, incompatible ones are dropped.

## Community Rules Migration

Same automatic migration as YAML imports.

<details>
<summary><strong>Technical Details</strong></summary>

1. UI posts to `/rules/migrate` with community rules
2. Server migrates rules based on configured server
3. UI receives migrated rules

</details>

Import any community rule regardless of origin server.

!!! note
    Community rules from much older Maintainerr versions may not work due to schema changes.

## Technical Implementation

### Rule Migration Detection

Rules are automatically detected by inspecting the `firstVal[0]` and `lastVal[0]` fields:
- `Application.PLEX` (value: 0)
- `Application.JELLYFIN` (value: 6)

Rules from Radarr, Sonarr, Tautulli, and Jellyseerr work with both Plex and Jellyfin.

!!! info
    - Overseerr only supports Plex.
    - Plex and Jellyfin rules are only migrated if compatible - see [Incompatible Features](#incompatible-features).

### Incompatible Property IDs

**Plex-only (28, 30-42):**
- 28, 30: Watchlist properties
- 31-38: External rating properties (IMDb, Rotten Tomatoes, TMDb)
- 39-42: Smart collection properties

**Jellyfin-only:**
- None currently
