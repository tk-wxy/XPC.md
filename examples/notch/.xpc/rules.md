# 铁律、死胡同与反查表 (rules.md)

> **信任级别：高（历史事实）。** 这里记录的是真实踩过的坑与验证失败的路线——
> **发生过就为真**，是本知识库最不会过期、最值钱的部分。动手前优先查这里，避免重复踩坑。
>
> **失效条目不要删。** 技术栈 / 依赖升级后，个别死胡同可能已不成立。此时用删除线标注，
> 保留历史避免有人再试：`~~原路线 → 原因~~ (已失效 YYYY-MM，因为……)`。
>
> **说明**：这是 notch 项目的硬约束规则集。违反铁律通常导致连锁 bug 或数据损坏。在开发过程中应严格遵守。

---

## 索引区（冷启动只读本区）

*每条一行：编号 | 一句话摘要 | 所属分节 | 是否高危。新增/修改/删除规则时必须同步更新本区。*

| 编号 | 摘要 | 分节 | 高危 |
|------|------|------|------|
| R1 | 笔记写入必须原子（temp+rename），禁止直接覆盖 | 写入 | 是 |
| R2 | Windows 上 rename 遇占用抛 EPERM，需带退避重试 | 写入 | 是 |
| R3 | git 操作全程串行，一把锁 + lockfile | 同步 | 是 |
| R4 | 存储层路径一律 POSIX，显示层才平台化 | 路径 | 是 |
| R5 | 配置目录用 env-paths，不硬编码 $HOME/~ | 配置 | 否 |
| R6 | Windows 终端输出显式 UTF-8，否则中文乱码 | 终端 | 否 |
| R7 | chokidar 在网络盘/WSL 需 usePolling 回退 | 监听 | 否 |
| R8 | pull 用 --rebase，禁止产生 merge commit 污染历史 | 同步 | 否 |

> **读取规则**：冷启动和会话开始时只扫本表 + 死胡同列表；
> 只有任务涉及某分节（或某条标记为高危）时，才读该分节全文。

---

## 铁律 (Iron Rules)
*按子系统或模块分节。每条规则格式：[动作] + [一行原因] + [DECISIONS §指针]*

### 写入 (store.ts，高危)
- [x] R1：笔记写入必须走原子写——`fs.writeFile(tmp)` → `fs.rename(tmp, target)`；绝不直接 `writeFile(target)`。原因：`watch` 或另一进程可能读到半写文件，崩溃则丢数据。详见 decisions.md §2
- [x] R2：`rename` 在 Windows 上遇目标被占用（杀毒/搜索索引/编辑器持句柄）会抛 `EPERM`/`EBUSY`——必须带指数退避重试（默认 5 次、20ms 起）。别把它当逻辑 bug 去「修」，是平台行为。详见 decisions.md §2

### 同步 (sync.ts，高危)
- [x] R3：所有 git 操作串行——进程内 `AsyncLock` + 仓库根 `.notch.lock` 跨进程锁；并发 `add/commit` 会撞 `.git/index.lock`。详见 decisions.md §1（反面）
- [x] R8：`pull` 一律 `--rebase`；merge commit 会把速记历史搅成网状、`log` 不可读。冲突时中止并提示用户手动解决，不自动 `-X theirs`。

### 路径 (paths.ts，高危)
- [x] R4：进入存储层/写进 git 的路径一律先 `toPosix()`（`\`→`/`、去盘符大小写敏感）；只有输出给用户看时才 `toPlatform()`。原因：Windows 上存了 `notes\2026\05.md`，到 macOS 上 clone 后路径失配、笔记「消失」。

### 配置 (config.ts)
- [x] R5：配置与默认仓库目录用 `env-paths('notch')`，**不要** `path.join(os.homedir(), '.notch')`。原因：Windows 无 `$HOME` 语义、macOS 应落 `~/Library/Application Support`。

### 终端 (cli.ts)
- [x] R6：Windows 下启动时确保 `process.stdout` 为 UTF-8（设置 `chcp 65001` 等价 / 写入前 `Buffer.from(s,'utf8')`）。原因：默认 GBK 代码页会让中文笔记回显乱码。

### 监听 (watch.ts)
- [x] R7：`chokidar` 初始化在检测到网络盘 / WSL 挂载（`\\` UNC 或 `/mnt/`）时启用 `usePolling:true`。原因：inotify/FSEvents 在这些挂载上不可靠、事件丢失。

---

## 死胡同 (Dead Ends)
*已验证失败的技术路线。避免重蹈覆辙。格式：路线 → 失败原因 → 验证日期*
*失效后不删，改用删除线：`~~路线 → 原因 → 日期~~ (已失效 YYYY-MM)`*

- 原生 `fs.watch` 做跨平台监听 → macOS 不给文件名、Windows `recursive` 行为不一致、Linux 需自己递归 → 换 `chokidar` → 2026-04-18
- `fs.writeFile(target)` 直接覆盖笔记 → `watch` 偶发读到空/半截文件、断电丢当日笔记 → 换原子写 temp+rename → 2026-04-22
- `path.join(os.homedir(), '.notch')` 定位配置 → Windows CI 上 `homedir()` 指向 `C:\Users\…` 但服务账户下为空、macOS 该进 Library → 换 `env-paths` → 2026-04-25
- git `pull`（默认 merge）自动同步 → 多机来回同步后 `log` 全是 merge commit、时间线错乱 → 改 `pull --rebase` → 2026-05-03

---

## 反查表 (Troubleshooting)
*遇到症状时，先查哪条铁律。格式：症状描述 → 对应铁律编号*

- 笔记偶发变空 / 被截断，尤其崩溃或 watch 高频写时 → 没走原子写，检查 R1
- Windows 上偶发 `EPERM: operation not permitted, rename` → 目标被占用，检查 R2 重试逻辑（不是逻辑 bug）
- 换机器 / 换 OS 后 clone，部分笔记「找不到」 → git 里存了平台分隔符路径，检查 R4
- 中文笔记在 Windows cmd/PowerShell 回显乱码 → stdout 未设 UTF-8，检查 R6
- `watch` 在共享盘 / WSL 里改文件不触发 → 未启用 polling 回退，检查 R7
- `sync` 报 `Unable to create '.git/index.lock': File exists` → 并发 git 操作，检查 R3 串行锁
- `log` 时间线错乱、满屏 merge commit → pull 产生了 merge，检查 R8
