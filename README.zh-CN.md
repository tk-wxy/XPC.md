**[English](./README.md)** · 简体中文

# XPC — 让 AI 别再重复踩同一个坑

> **你的 AI 每开一次新对话就把项目忘光、把上次踩过的坑重踩一遍；换个模型更是彻底失忆。**
> XPC 是一套轻量的归档式提示词框架——**本质就是几个 Markdown 文件**。把项目意图、踩坑经验、
> 架构决策沉淀进去，让**任意** AI agent 冷启动后几分钟内接手，且不再重复犯错。

XPC = **Cross-Platform Coding prompt framework**。一句话：**把 AI 的经验从「一次性的对话」变成「可积累的资产」。**
它没有必须安装的工具——**就是 md 文件 + AI 照着跑**。

---

## 怎么用（clone → 一句话）

**1. 把框架 clone 进你的新项目目录**，删掉不用的平台入口文件：

```bash
git clone <this-repo> my-project
cd my-project
rm -rf .git && git init      # 换成你自己项目的 git 仓库
```

**2. 打开你的 AI agent，说一句：**

> 我的项目需求是……（描述你的项目），请初始化。

**就这样。** AI 一开对话会自动读入口文件（`CLAUDE.md` / `AGENTS.md` / 你所在平台的入口），
其中的〈初始化引导〉会把它接到 `.xpc/init.md` 的初始化协议，它会：

- 从你的需求里提取信息，**缺的（技术栈、高危区）主动问你**，不臆测
- 判断项目类型（全新 / 已有代码），据此烘焙 `manifest.md`（项目宪法）、填掉所有 `{{占位符}}`
- 初始化 `memory.md §0`；`rules.md` / `decisions.md` 保持空骨架（规则从真实踩坑中生长，不编造）
- **复述宪法请你确认**，确认后才进入开发

> **初始化是一次性的**：AI 把通用骨架消化成本项目专属结构——消耗掉一次性脚手架
> （`.xpc/init.md`、填充占位、本 README、`examples/`），保留各司其职的 md + 持久约束。
> 之后每次会话，AI 冷启动读 `.xpc/`，按 `.xpc/workflow.md` 工作；收工时对它说「收尾」，它更新知识库。

---

## 先看「填满后」长什么样

空模板很难想象用起来的感觉。先看一个**已经迭代过若干会话的真实感示例**：

👉 **[`examples/notch/`](./examples/notch/)** —— 一个虚构跨平台 CLI 工具的完整 XPC 知识库。

重点看 **[`examples/notch/.xpc/rules.md`](./examples/notch/.xpc/rules.md)** 里那张 `症状 → 铁律` 反查表——
这些坑，新 agent 本来要花好几轮才能重新踩明白；有了这张表，它开局就知道。**这就是 XPC 的价值内核。**

---

## 文件各司其职（`.xpc/`）

| 文件 | 角色 | 信任级别 |
|------|------|---------|
| `manifest.md` | 项目宪法：使命 / 技术栈 / 架构不变量 / 高危区（冷启动第一站） | **高**（人工确认过） |
| `rules.md` | 踩坑 / 死胡同 / `症状→铁律` 反查表 | **高**（发生过就为真——**最值钱**） |
| `decisions.md` | 架构决策根因 + 证据 | **高** |
| `memory.md` | 现状快照（§0 只 4 条 bullet）+ 最近会话（§0A ≤3） | **低（易过期）** 只作方向线索，用前核对源码 |
| `workflow.md` | 开发流程协议（冷启动 / 会话 / 任务结束 的唯一标准） | — |
| `history.md` | 历史归档（默认不读，考古用） · `garden.md` 园丁整理 · `init.md` 一次性初始化 | — |

> **信任级别是 XPC 的核心姿态**：踩坑/决策是不腐烂的历史事实（最值钱），现状快照易过期（只当线索）。
> 这破解了「过期内容被当真相」这个唯一会让参考变有害的坑。

---

## 诚实的边界（先说清楚，别期望错）

- ✅ **适合**：单人 / 小团队 + AI agent 长期迭代、平台怪癖多、经验知识密集的项目。
- ⚠️ **它不自维护**：XPC 假设**有人当园丁**定期修剪（`.xpc/garden.md`）。知识库会随开发变脏、过期——
  这是正常的，靠定期整理，不靠祈祷 AI 每次都维护完美。
- ⚠️ **价值后置**：新项目前期薄，真正值钱的踩坑库要迭代久了、真受过苦才厚。
- ❌ **不适合**：纯人工开发、不用 AI agent、或一次性的短脚本。

## 无缝切换

换 agent / 换模型不失忆：新 agent 读自己的入口文件 → 执行冷启动协议 → 先读 `manifest.md`
（锁定意图不偏航）→ 再读 `memory.md`（当前状态）→ 即刻接手，无需重新初始化。

## 支持的平台入口

`AGENTS.md`（跨平台标准，推荐）· `CLAUDE.md` · `GEMINI.md` · `.cursor/rules/*.mdc` · `.windsurfrules` ·
`.github/copilot-instructions.md` · `.clinerules/core.md` · `.roo/rules-code/core.md` · `CONVENTIONS.md`（Aider）·
`augment-guidelines.md` · `.amazonq/rules/core.md`。只保留你用的，其余删掉。

## 轻量用法

小工具 / 短期项目不必上全套：只留 `AGENTS.md` + `.xpc/manifest.md` + `.xpc/rules.md` 即可——
一个项目宪法 + 一个踩坑库，已经能拿到 XPC 八成的价值。

## License

MIT
