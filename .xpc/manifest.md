# 项目身份 (manifest.md)

> **本文件由初始化阶段生成，之后极少修改。**
> 它是所有 AI agent 的共识锚点——无论哪个 agent、哪个平台、哪次会话，
> 都从这里获取项目的不可变意图。修改本文件等同于修改项目宪法，需慎重。

---

## 项目使命

- **名称**：{{PROJECT_NAME}}
- **一句话定位**：{{PROJECT_DESCRIPTION}}
- **核心目标**：{{CORE_GOALS}}
- **非目标（明确不做的事）**：{{NON_GOALS}}

## 技术栈（锁定）

{{TECH_STACK}}

## 架构不变量

> 以下是项目的架构基石，任何 agent 不得擅自推翻。如需变更，必须先在
> decisions.md 中新增 § 记录根因，并获得用户确认后才能修改本文件。

- {{ARCHITECTURE_INVARIANTS}}

## 高危区域

> 改动这些区域容易引发连锁 bug，必须格外谨慎。

- {{HIGH_RISK_AREAS}}

## 项目结构

{{PROJECT_STRUCTURE}}

## 常用命令

{{COMMON_COMMANDS}}

---

> **本文件的修改规则**：
> 1. 初始化后，只有在架构发生根本性变化时才允许修改
> 2. 修改前必须在 decisions.md 中记录变更根因
> 3. 修改后所有平台入口文件需同步更新
