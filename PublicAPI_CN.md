# EXBoss 公开事件 API

> **版本**：适用于 EXBoss 12.0+
>
> 本文档描述 EXBoss 对外公开的事件接口。第三方插件可通过 ExwindCore 提供的事件系统订阅这些事件，无需复制任何 EXBoss 内部逻辑。

---

## 前置依赖

你的插件必须声明对 **ExwindCore** 的依赖。`ExwindTools` 是 ExwindCore 暴露的全局对象，是注册事件的唯一入口。

```lua
-- YourAddon.toc
## Dependencies: ExwindCore, EXBoss
```

---

## 注册与注销

```lua
-- 注册
ExwindTools:RegisterEvent("EXBOSS_TRASH_OBSERVED_CAST_START", "MyAddon_Key", function(event, payload)
    -- payload 字段见下方说明
end)

-- 注销
ExwindTools:UnregisterEvent("EXBOSS_TRASH_OBSERVED_CAST_START", "MyAddon_Key")
```

- 第二参数 `owner` 是隔离键，建议使用插件名。同一 owner 对同一事件重复注册，只保留最后一个回调。
- 回调内抛出的错误会被 `pcall` 捕获并打印，不会影响其他订阅者。

---

## 公开事件列表

### 1. `EXBOSS_TRASH_OBSERVED_CAST_START`

**触发时机**：小怪施法条被检测到（普通施法或引导）。

| 字段 | 类型 | 说明 |
|---|---|---|
| `unit` | `string` | 姓名版 unit token，例如 `"nameplate3"` |
| `npcID` | `number` | NPC ID |
| `mapID` | `number` | 副本地图 ID |
| `spellID` | `number` | 法术 ID |
| `castKind` | `string` | `"cast"` 或 `"channel"` |
| `castBarID` | `number` | 施法实例关联 token，用于匹配对应的 stop 事件 |
| `startedAt` | `number` | `GetTime()` 时间戳 |
| `totalDuration` | `number\|nil` | 施法总时长（秒），nil 表示未知 |
| `displayName` | `string` | 技能显示名称 |

---

### 2. `EXBOSS_TRASH_CASTBAR_STOP`

**触发时机**：小怪施法条结束（成功、被打断或超时）。

| 字段 | 类型 | 说明 |
|---|---|---|
| `unit` | `string` | 姓名版 unit token |
| `npcID` | `number` | NPC ID |
| `mapID` | `number` | 副本地图 ID |
| `spellID` | `number` | 法术 ID |
| `castBarID` | `number` | 与 `CAST_START` 对应的关联 token |
| `castKind` | `string` | `"cast"` 或 `"channel"` |
| `stopReason` | `string` | `"succeeded"`、`"interrupted"`、`"failed"` 之一 |
| `startedAt` | `number` | 施法开始时间戳 |
| `stoppedAt` | `number` | 施法结束时间戳 |

---

### 3. `EXBOSS_BOSS_OBSERVED_CAST_START`

**触发时机**：Boss 施法条被检测到。

| 字段 | 类型 | 说明 |
|---|---|---|
| `unit` | `string` | Boss unit token |
| `spellID` | `number\|nil` | 法术 ID（来自 EXBoss 时间轴配置，非秘密值） |
| `encounterID` | `number\|nil` | 遭遇战 ID |
| `eventID` | `number\|nil` | 时间轴事件 ID |
| `castKind` | `string` | `"cast"` 或 `"channel"` |
| `castBarID` | `number` | 施法实例关联 token |
| `startedAt` | `number` | 施法开始时间戳 |
| `totalDuration` | `number` | 施法总时长（秒） |
| `displayName` | `string` | 技能显示名称 |
| `progressDisplayName` | `string` | 进度条显示名称 |
| `source` | `string` | 固定为 `"boss"` |

---

### 4. `EXBOSS_BOSS_OBSERVED_CAST_STOP`

**触发时机**：Boss 施法条结束。

| 字段 | 类型 | 说明 |
|---|---|---|
| `unit` | `string` | Boss unit token |
| `spellID` | `number\|nil` | 法术 ID |
| `encounterID` | `number\|nil` | 遭遇战 ID |
| `eventID` | `number\|nil` | 时间轴事件 ID |
| `castBarID` | `number` | 与 `CAST_START` 对应的关联 token |
| `castKind` | `string` | `"cast"` 或 `"channel"` |
| `startedAt` | `number` | 施法开始时间戳 |
| `stoppedAt` | `number` | 施法结束时间戳 |
| `totalDuration` | `number` | 施法总时长 |
| `displayName` | `string` | 技能显示名称 |
| `source` | `string` | 固定为 `"boss"` |

---

### 5. `EXBOSS_TIMELINE_HINT`

**触发时机**：时间轴事件即将发生（预警阈值触发，通常在施法前若干秒）。

| 字段 | 类型 | 说明 |
|---|---|---|
| `eventID` | `number` | 时间轴事件 ID |
| `spellID` | `number\|nil` | 法术 ID（可能为 nil） |
| `encounterID` | `number\|nil` | 遭遇战 ID |
| `threshold` | `number` | 触发此 hint 的剩余秒数阈值 |
| `remaining` | `number` | 触发时剩余时间（秒） |
| `scheduledAt` | `number` | 技能预计触发的绝对时间戳 |
| `displayName` | `string` | 技能显示名称 |
| `source` | `string\|nil` | 来源标识 |

---

### 6. `EXBOSS_MOB_RESET`

**触发时机**：小怪姓名版消失（死亡或离开范围）。消费者应清理与该 unit 关联的所有 UI。

| 字段 | 类型 | 说明 |
|---|---|---|
| `unit` | `string` | 姓名版 unit token |
| `npcID` | `number\|nil` | NPC ID（若推理未完成则为 nil） |
| `mapID` | `number\|nil` | 副本地图 ID |
| `reason` | `string` | `"died"` 或 `"removed"`（离开范围） |

---

## 典型用法

```lua
local unitToNPC = {}

ExwindTools:RegisterEvent("EXBOSS_TRASH_OBSERVED_CAST_START", "MyBridge", function(_, p)
    unitToNPC[p.unit] = p.npcID
    -- 转发给 WeakAuras / MRT / 其他系统
end)

ExwindTools:RegisterEvent("EXBOSS_TRASH_CASTBAR_STOP", "MyBridge", function(_, p)
    -- 用 p.castBarID 匹配对应的 CAST_START
end)

ExwindTools:RegisterEvent("EXBOSS_MOB_RESET", "MyBridge", function(_, p)
    unitToNPC[p.unit] = nil
    -- 清理锚定到 p.unit 的所有 UI
end)
```

---

## 稳定性承诺

- 以上事件名和 payload 字段为**稳定公开 API**，版本升级时保持向后兼容。
- `runtime` 等内部引用字段**不属于公开 API**，随时可能变更，请勿使用。
- 以上列表之外的 `EXBOSS_*` 事件为内部实现，无稳定性保证。

---

有问题或新需求请在 EXBoss 官方渠道反馈。
