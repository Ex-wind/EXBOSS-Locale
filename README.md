# EXBOSS-Locale

[EXBoss](https://github.com/Ex-wind) 插件的本地化文件仓库，包含 zhCN / zhTW / enUS 及其他语言占位文件。

---

## 参与翻译 / How to Contribute

### 方式一：提交 Pull Request（推荐）

如果你熟悉 GitHub，欢迎直接 Fork 本仓库，修改对应语言文件后提交 PR。  
每个语言文件都以 `语言代码.lua` 命名，例如 `enUS.lua`、`deDE.lua`。

格式示例：

```lua
L["副本"] = "Dungeons"   -- 把右侧改成你的语言
```

### 方式二：在 Issues 提交内容

不熟悉 GitHub 也没关系。  
点击上方 **Issues → New Issue**，把你修改或补充的文本内容粘贴进去，说明是哪个语言即可，作者会手动合并。

### 方式三：私信联系作者

也可以在以下平台私信联系：

- Bilibili：[@绿色歹人](https://space.bilibili.com/)
- 直播间留言

---

## 关于本仓库 / About This Repo

由于一些历史原因，**目前只有插件的本地化文件放在公开仓库**，插件的其余功能代码暂不开源。

EXBoss 本赛季起步较晚，且一开始没有预料到插件会发展得这么快，留下了一些历史遗留的架构问题。如果你对插件的其他模块代码有疑问，欢迎私聊作者，我们会尽力解答。

我们计划在 **12.1 版本进行一次较大的架构重构**，届时会重新整理仓库结构，也会同步考虑更多开源的可能性。感谢你的理解与支持。

---

## 文件说明 / Files

| 文件 | 语言 | 状态 |
|------|------|------|
| `zhCN.lua` | 简体中文 | ✅ 完整 |
| `zhTW.lua` | 繁体中文 | ✅ 自动转换自 zhCN |
| `enUS.lua` | 英文 | ✅ 完整 |
| `koKR.lua` | 한국어 | 🔤 英文占位，欢迎提交翻译 |
| `ruRU.lua` | Русский | 🔤 英文占位，欢迎提交翻译 |
| `deDE.lua` | Deutsch | 🔤 英文占位，欢迎提交翻译 |
| `frFR.lua` | Français | 🔤 英文占位，欢迎提交翻译 |
| `esES.lua` | Español (ES) | 🔤 英文占位，欢迎提交翻译 |
| `esMX.lua` | Español (MX) | 🔤 英文占位，欢迎提交翻译 |
| `ptBR.lua` | Português | 🔤 英文占位，欢迎提交翻译 |
| `itIT.lua` | Italiano | 🔤 英文占位，欢迎提交翻译 |

---

*感谢所有参与翻译与测试的朋友。*
