# 历史归档 (history.md)

> **说明**：本文件为 `memory.md` §0A 中老化条目（超出滚动窗口）的归档区。
> 默认**不读**，仅在需要考古时通过 `grep` 搜索。按时间倒序排列。

## 2026年04月

### 会话 2026-04-25 14:10 - 配置定位改 env-paths
- 现象：Windows CI（windows-latest）上所有涉及配置读写的用例整段失败。
- 根因：`os.homedir()` 在 runner 服务账户下为空 → 配置路径变成相对目录。
- 改法：切 `env-paths('notch')`，立 rules R5 + decisions §3。CI 转绿。

### 会话 2026-04-22 09:30 - 笔记写入改原子写
- 现象：`watch` 压测时偶发读到空文件；一次强制断电后当日笔记清零。
- 根因：`fs.writeFile(target)` 有半写窗口。
- 改法：temp + rename 原子写，立 rules R1 + decisions §2；死胡同记「直接覆盖」一条。

### 会话 2026-04-18 11:00 - 监听从 fs.watch 换 chokidar
- 现象：macOS 上 watch 拿不到变更文件名，Windows 深层目录漏事件。
- 改法：换 chokidar，立 decisions §1 + 死胡同「原生 fs.watch」一条。
