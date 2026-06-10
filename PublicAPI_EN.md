# EXBoss Public Event API

> **Version**: Applies to EXBoss 12.0+
>
> This document describes EXBoss's public event interface. Third-party addons can subscribe to these events via ExwindCore's event system without copying any EXBoss internal logic.

---

## Prerequisites

Your addon must declare a dependency on **ExwindCore**. `ExwindTools` is the global object exposed by ExwindCore and is the only entry point for event registration.

```lua
-- YourAddon.toc
## Dependencies: ExwindCore, EXBoss
```

---

## Registration & Unregistration

```lua
-- Register
ExwindTools:RegisterEvent("EXBOSS_TRASH_OBSERVED_CAST_START", "MyAddon_Key", function(event, payload)
    -- see payload fields below
end)

-- Unregister
ExwindTools:UnregisterEvent("EXBOSS_TRASH_OBSERVED_CAST_START", "MyAddon_Key")
```

- The second argument `owner` is an isolation key. Use your addon name. Registering the same event with the same owner overwrites the previous callback.
- Errors thrown inside callbacks are caught by `pcall` and printed; they do not affect other subscribers.

---

## Public Event List

### 1. `EXBOSS_TRASH_OBSERVED_CAST_START`

**Fired when**: A trash mob cast bar is detected (cast or channel).

| Field | Type | Description |
|---|---|---|
| `unit` | `string` | Nameplate unit token, e.g. `"nameplate3"` |
| `npcID` | `number` | NPC ID |
| `mapID` | `number` | Dungeon map ID |
| `spellID` | `number` | Spell ID |
| `castKind` | `string` | `"cast"` or `"channel"` |
| `castBarID` | `number` | Correlation token to match against the corresponding stop event |
| `startedAt` | `number` | `GetTime()` timestamp |
| `totalDuration` | `number\|nil` | Total cast duration in seconds, nil if unknown |
| `displayName` | `string` | Spell display name |

---

### 2. `EXBOSS_TRASH_CASTBAR_STOP`

**Fired when**: A trash mob cast bar ends (succeeded, interrupted, or timed out).

| Field | Type | Description |
|---|---|---|
| `unit` | `string` | Nameplate unit token |
| `npcID` | `number` | NPC ID |
| `mapID` | `number` | Dungeon map ID |
| `spellID` | `number` | Spell ID |
| `castBarID` | `number` | Correlation token matching the corresponding `CAST_START` |
| `castKind` | `string` | `"cast"` or `"channel"` |
| `stopReason` | `string` | One of `"succeeded"`, `"interrupted"`, `"failed"` |
| `startedAt` | `number` | Cast start timestamp |
| `stoppedAt` | `number` | Cast end timestamp |

---

### 3. `EXBOSS_BOSS_OBSERVED_CAST_START`

**Fired when**: A boss cast bar is detected.

| Field | Type | Description |
|---|---|---|
| `unit` | `string` | Boss unit token |
| `spellID` | `number\|nil` | Spell ID (from EXBoss timeline config, not a secret value) |
| `encounterID` | `number\|nil` | Encounter ID |
| `eventID` | `number\|nil` | Timeline event ID |
| `castKind` | `string` | `"cast"` or `"channel"` |
| `castBarID` | `number` | Correlation token |
| `startedAt` | `number` | Cast start timestamp |
| `totalDuration` | `number` | Total cast duration in seconds |
| `displayName` | `string` | Spell display name |
| `progressDisplayName` | `string` | Progress bar display name |
| `source` | `string` | Always `"boss"` |

---

### 4. `EXBOSS_BOSS_OBSERVED_CAST_STOP`

**Fired when**: A boss cast bar ends.

| Field | Type | Description |
|---|---|---|
| `unit` | `string` | Boss unit token |
| `spellID` | `number\|nil` | Spell ID |
| `encounterID` | `number\|nil` | Encounter ID |
| `eventID` | `number\|nil` | Timeline event ID |
| `castBarID` | `number` | Correlation token matching the corresponding `CAST_START` |
| `castKind` | `string` | `"cast"` or `"channel"` |
| `startedAt` | `number` | Cast start timestamp |
| `stoppedAt` | `number` | Cast end timestamp |
| `totalDuration` | `number` | Total cast duration |
| `displayName` | `string` | Spell display name |
| `source` | `string` | Always `"boss"` |

---

### 5. `EXBOSS_TIMELINE_HINT`

**Fired when**: A timeline event is approaching (pre-alert threshold, typically several seconds before the cast).

| Field | Type | Description |
|---|---|---|
| `eventID` | `number` | Timeline event ID |
| `spellID` | `number\|nil` | Spell ID (may be nil) |
| `encounterID` | `number\|nil` | Encounter ID |
| `threshold` | `number` | Remaining seconds threshold that triggered this hint |
| `remaining` | `number` | Remaining time in seconds at the moment this event fired |
| `scheduledAt` | `number` | Absolute timestamp when the event is scheduled to fire |
| `displayName` | `string` | Spell display name |
| `source` | `string\|nil` | Source identifier |

---

### 6. `EXBOSS_MOB_RESET`

**Fired when**: A trash mob nameplate disappears (died or left range). Consumers should clean up any UI anchored to this unit.

| Field | Type | Description |
|---|---|---|
| `unit` | `string` | Nameplate unit token |
| `npcID` | `number\|nil` | NPC ID (nil if inference not yet completed) |
| `mapID` | `number\|nil` | Dungeon map ID |
| `reason` | `string` | `"died"` or `"removed"` (left range) |

---

## Typical Usage

```lua
local unitToNPC = {}

ExwindTools:RegisterEvent("EXBOSS_TRASH_OBSERVED_CAST_START", "MyBridge", function(_, p)
    unitToNPC[p.unit] = p.npcID
    -- forward to WeakAuras / MRT / other systems
end)

ExwindTools:RegisterEvent("EXBOSS_TRASH_CASTBAR_STOP", "MyBridge", function(_, p)
    -- use p.castBarID to match the corresponding CAST_START
end)

ExwindTools:RegisterEvent("EXBOSS_MOB_RESET", "MyBridge", function(_, p)
    unitToNPC[p.unit] = nil
    -- clean up all UI anchored to p.unit
end)
```

---

## Stability Guarantee

- The event names and payload fields listed above are **stable public API** and will remain backward-compatible across versions.
- Internal reference fields such as `runtime` are **not part of the public API** and may change at any time. Do not use them.
- Any `EXBOSS_*` events not listed here are internal implementation details with no stability guarantee.

---

For questions or new requirements, please reach out through EXBoss official channels.
