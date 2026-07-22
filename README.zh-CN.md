**[English](./README.md)** · 简体中文

# XPC — 给你的 AI 持久的项目记忆

**AI 每开新对话就忘光项目、把踩过的坑重踩一遍、换个模型彻底失忆。**
XPC 用几个 Markdown 文件解决它——不装工具、不跑命令。把项目意图、踩坑经验、架构决策沉淀下来，让任意 agent 接着上一个继续干。

## 一句话上手

```bash
git clone https://github.com/tk-wxy/XPC.md.git my-project
cd my-project && rm -rf .git && git init
```

然后对你的 agent 说：

> 我的项目需求是……（描述），请初始化。

它会读入口文件（`CLAUDE.md` / `AGENTS.md` …）、执行 `.xpc/init.md`、缺什么问你、烘焙项目宪法、复述确认。此后每次会话都从 `.xpc/` 冷启动——**并按你的语言工作**，中文说「请初始化」就得到中文知识库。

## 看填满的样子

**→ [`examples/notch/.xpc/rules.md`](./examples/notch/.xpc/rules.md)** —— 那张 `症状 → 铁律` 表就是精髓：新 agent 本要踩好几轮才懂的坑，开局就知道。

## 文件分工（`.xpc/`）

| 文件 | 角色 | 信任 |
|------|------|------|
| `manifest.md` | 项目宪法——使命 / 技术栈 / 不变量 / 高危区 | **高** |
| `rules.md` | 踩坑、死胡同、`症状→铁律` 表 | **高**——最值钱 |
| `decisions.md` | 每个选择为什么，带证据 | **高** |
| `memory.md` | 现状快照(§0) + 最近会话 | **低**——用前先核对 |
| `workflow.md` · `history.md` · `garden.md` | 开发协议 · 归档 · 园丁整理 | — |

**信任级别是核心思想**：踩坑是永久事实（可信），现状快照会过期（只当线索）。就这一个区分，避免了过期笔记误导下一个 agent。

## 采用前先知道

- ✅ 最适合：单人 / 小团队 + agent 长期迭代、平台怪癖多、知识密集的项目。
- ⚠️ 它不自维护——靠人定期修剪（`.xpc/garden.md`）；价值随迭代累积，不在第一天。
- ❌ 不适合：一次性脚本，或不用 AI agent 的开发。

## 补充

- **平台**：内置 AGENTS.md（推荐）、Claude、Gemini、Cursor、Copilot、Windsurf、Cline、Roo、Aider、Augment、Amazon Q 的入口——留你用的，删其余。
- **想极简**：只留 `AGENTS.md` + `.xpc/manifest.md` + `.xpc/rules.md` 就能拿到八成价值。

MIT
