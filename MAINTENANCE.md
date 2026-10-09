# 内核维护路线

基于 `oneplus/mt6991_v_15.0.2_ace5_ultra_6.6.89` 分支，6.6.89。

## 约束

- **所有调试/安全相关 CONFIG 一律不改，不关、不调。** 包括但不限于：KASAN / KASAN_HW_TAGS / PAGE_OWNER / PAGE_PINNER / KFENCE / UBSAN / DEBUG_MEMORY_INIT。
- 实测结论：这些项即便上游/社区文档说"可关"，在本设备上关掉会无法开机。不逐条赌，全部保留现状。
- SUSFS / KSU / HMBIRD 均为构建时外部 patch 注入，不在内核源码仓库内，源码层不动。

## 路线

### 1. 安全（最高优先级）

定期从 stable 6.6.y backport CVE 修复。重点关注 UAF、race、提权类。

已合入：
- CVE-2026-43499 rtmutex（含后续 40a25d59e85b 空指针守卫）
- CVE-2026-46242 epoll ep_remove UAF + ep_free kfree_rcu
- CVE-2026-53266 ebt_snat ARP 写越界
- CVE-2026-23111 nftables catchall genmask 反转
- hmbird.h 序列点 UB 修复

跟进方式：拉取 stable 6.6.y changelog，逐条核对是否已合，未合的按本仓库风格适配后提交。适配要点见下文「backport 规范」。

### 2. 性能

CONFIG 调试开关一律不动（见约束）。

I/O 与压缩：
- 追 ADIOS 调度器上游更新
- 追 lz4 / zstd 上游 release
- ZRAM 改 `=y` 省模块加载，评估 ZRAM_WRITEBACK

### 3. 反检测

内核层不直接改。隐藏能力由 SUSFS（构建时注入）+ 运行时配置提供。运行时配置入口：
```
sudo su -c "/data/adb/ksu/bin/ksu_susfs config list_all"
```
关键开关：
- `hide_sus_mnts_for_non_su_procs` = true
- `sus_path` 隐藏 /data/adb 及子路径
- `sus_map` 隐藏 ksud / ksu_susfs 等

uname 伪装由 SUSFS `set_uname` 完成，`/proc/version` 串由构建时 `KBUILD_BUILD_USER/HOST` 写死。

### 4. 功能增强（按需）

按需求评估，不主动堆砌：
- 文件系统：BTRFS / NTFS3 / EROFS 压缩
- 网络：WireGuard、DCTCP / Vegas
- 容器：cgroup v2、netns 完善
- 硬化：CFI_CLANG、INIT_STACK_ALL_ZERO、FORTIFY_SOURCE

### 5. 上游同步节奏

| 项目 | 频率 | 来源 |
|---|---|---|
| stable 6.6.y CVE | 每月 | kernel.org 6.6.y changelog |
| BakaSU / SUSFS | 跟发布 | Baka-SU/BakaSU |
| lz4 / zstd | 半年 | 上游 release |
| HMBIRD | 不定期 | OPPO 提交 |

## backport 规范

1. 拿到上游 commit 全文（含 `Fixes:` 行），不只看 CVE 描述。
2. 检查是否有后续修复（`Fixes: <sha>` 指向刚合入的那条）。
3. 适配本树差异：
   - 缺失的宏（如 `__free(fput)` 是 6.18+）→ 手工等价改写
   - 缺失的函数拆分（如 `ep_remove_file/ep_remove_epi`）→ 内联到现有函数
   - 注释精简，不照搬上游长段
4. 编译验证：`make O=/tmp/kbuild ARCH=arm64 CC=aarch64-linux-gnu-gcc KCFLAGS+=-Wno-error <target>.o`
   - 必须带 `-Wno-error`，因 `include/linux/sched/hmbird.h` 有既有 sequence-point 警告。
   - 无 vendor defconfig 时用 `gki_defconfig` + `./scripts/config -m NF_TABLES` 补依赖。
5. 提交者只保留本人，不加 Co-Authored-By。

## 构建脚本协同

内核源码仓库合入修复后，构建仓库（本仓库）的 builder / workflow 里对应的 `wget + patch` 步骤必须删除，否则 `set -e` 下 patch 失败会中断构建。已处理：CVE-2026-43499 的 wget+patch 步骤已从 builder.sh 和 workflow 中移除。
