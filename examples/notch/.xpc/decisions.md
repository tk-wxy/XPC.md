# 架构决策与踩坑根因 (decisions.md)

> **信任级别：高（附证据的历史决策）。** 记录「为什么这么选」，只增不改。
> 若某决策被后续推翻，不删旧 §，新增一个 § 记录新决策并注明「取代 §N」。

## 目录 (决策摘要)
*每 § 一行结论摘要，扫目录即知是否需要阅读该节内容。*

- §1 文件监听选 chokidar 而非原生 fs.watch（跨平台一致性 + polling 回退）
- §2 笔记写入用「临时文件 + rename」原子写，并对 Windows EPERM 重试
- §3 配置/仓库目录用 env-paths，不手拼 $HOME

---

## 决策详情

### §1 文件监听：chokidar vs 原生 fs.watch
- **最终方案**：用 `chokidar`，并在网络盘/WSL 挂载上启用 `usePolling`（R7）。
- **根因与证据**：原生 `fs.watch` 三平台语义不一致——macOS 的 `change` 事件常不带 filename；Windows `recursive:true` 对深层目录偶发不触发；Linux 不支持 recursive、要自己递归 add watch。实测在 `test/watch.spec.ts` 里同一组改动，fs.watch 在 macOS 漏报文件名 3/10 次，chokidar 10/10 稳定。
- **否决的备选及原因**：① 自己封装 `fs.watch` + 递归——把跨平台差异全背到自己身上，正是死胡同记录里那条；② `@parcel/watcher`（更快的原生实现）——需预编译二进制、增加安装体积与跨平台构建复杂度，对速记工具的量级不划算。

### §2 笔记写入：原子写 + Windows rename 重试
- **最终方案**：`writeFile(target.tmp-<rand>)` → `fsync` → `rename(tmp, target)`；Windows 上 rename 抛 `EPERM`/`EBUSY` 时指数退避重试（5 次，20/40/80…ms）。
- **根因与证据**：直接 `writeFile(target)` 存在「已截断、未写完」的时间窗，`watch` 或另一 `notch` 进程会读到空/半文件；断电则丢当日笔记。改原子写后，`test/store.concurrent.spec.ts` 里 100 次并发写读零损坏。Windows 的 EPERM 来自杀毒/搜索索引器短暂持有目标文件句柄——rename 本身是对的，只需重试；实测重试后失败率从 ~0.5% 降到 0。
- **否决的备选及原因**：① 文件锁（`proper-lockfile`）——原子 rename 已足够、再加锁是过度设计；② 写前 `unlink` 目标再 rename——制造了更大的「目标不存在」窗口，反而更糟。

### §3 配置定位：env-paths vs 手拼 $HOME
- **最终方案**：`env-paths('notch', { suffix: '' })` 决定配置与默认仓库位置。
- **根因与证据**：`os.homedir()` 在 Windows 服务账户 / CI runner 下可能为空或指向意外目录；macOS 惯例是 `~/Library/Application Support` 而非 `~/.notch`。env-paths 一处解决三平台惯例。CI（GitHub Actions windows-latest）上手拼 `$HOME` 版本曾整段失败，切 env-paths 后绿。
- **否决的备选及原因**：`XDG_CONFIG_HOME` 手动实现——只覆盖 Linux 惯例，Windows/macOS 仍要特判，等于重造 env-paths。
