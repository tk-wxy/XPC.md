# 项目身份 (manifest.md)

> **本文件由初始化阶段生成，之后极少修改。**
> 它是所有 AI agent 的共识锚点——无论哪个 agent、哪个平台、哪次会话，
> 都从这里获取项目的不可变意图。修改本文件等同于修改项目宪法，需慎重。

---

## 项目使命

- **名称**：notch
- **一句话定位**：跨平台命令行速记工具——一条命令把带时间戳的想法追加进本地 git 仓库，`watch` 模式自动同步。
- **核心目标**：让「记一条想法」的摩擦趋近于零（`notch "..."` 即存即走）；笔记以纯 Markdown 落地、用 git 做历史与多机同步；跨 Windows / macOS / Linux 行为一致。
- **非目标（明确不做的事）**：不做云服务/账号体系（同步交给用户自己的 git remote）；不做富文本/编辑器（只负责追加与检索）；不做实时协作。

## 技术栈（锁定）

- 运行时：Node.js 20 (ESM) + TypeScript 5
- CLI：`commander`
- 文件监听：`chokidar`（**不用原生 `fs.watch`**，见 decisions §1）
- Git 操作：`simple-git`
- 配置定位：`env-paths`（**不手拼 `$HOME`**，见 decisions §3）
- 打包：`tsup`；测试：`vitest`

## 架构不变量

> 以下是项目的架构基石，任何 agent 不得擅自推翻。如需变更，必须先在
> decisions.md 中新增 § 记录根因，并获得用户确认后才能修改本文件。

- **所有笔记写入必须是原子的**（临时文件 + rename），绝不 `writeFile` 直接覆盖（见 rules §写入）。
- **git 同步全程串行**：任一时刻只有一个 git 操作在跑（一把进程内锁 + 跨进程 lockfile）。
- **存储层路径一律 POSIX**（`/` 分隔、小写盘符无关），显示层才转平台分隔符。
- **纯 CLI、无常驻守护进程**：`watch` 是前台进程，退出即停，状态只落在 git 仓库里。

## 高危区域

> 改动这些区域容易引发连锁 bug，必须格外谨慎。

- `src/store.ts`（原子写 + Windows rename 重试）
- `src/sync.ts`（git 并发/锁/冲突处理）
- `src/paths.ts`（跨平台路径归一化，一处错则换机器后全盘失配）

## 项目结构

```
src/
  cli.ts       # commander 入口，注册子命令（add/watch/sync/log）
  store.ts     # 笔记读写：原子写（temp+rename+Windows 重试）、按日分文件
  sync.ts      # git 同步：add/commit/pull --rebase/push，串行锁
  watch.ts     # chokidar 监听 + 防抖批量 commit
  config.ts    # 配置定位（env-paths）+ 仓库路径解析
  paths.ts     # 跨平台路径归一化（存储 POSIX / 显示平台化）
  index.ts     # 库入口
test/          # vitest 用例
```

## 常用命令

```bash
npm install
npm run dev -- add "一条想法"   # 本地跑 CLI
npm test                        # vitest
npm run build                   # tsup 打包到 dist/
```

---

> **本文件的修改规则**：
> 1. 初始化后，只有在架构发生根本性变化时才允许修改
> 2. 修改前必须在 decisions.md 中记录变更根因
> 3. 修改后所有平台入口文件需同步更新
