# 内核维护路线

基于 `oneplus/mt6991_v_15.0.2_ace5_ultra_6.6.89` 分支，6.6.89。

## 约束

- **所有调试/安全相关 CONFIG 一律不改，不关、不调。** 包括但不限于：KASAN / KASAN_HW_TAGS / PAGE_OWNER / PAGE_PINNER / KFENCE / UBSAN / DEBUG_MEMORY_INIT。
- 实测结论：这些项即便上游/社区文档说"可关"，在本设备上关掉会无法开机。不逐条赌，全部保留现状。
- SUSFS / KSU / HMBIRD 均为构建时外部 patch 注入，不在内核源码仓库内，源码层不动。

## 已完成

### 安全 backport（22 项）

CVE 修复（commit `f5d6ab3fb`）：
- CVE-2026-43499 rtmutex（含后续 40a25d59e85b 空指针守卫）
- CVE-2026-46242 epoll ep_remove UAF + ep_free kfree_rcu
- CVE-2026-53266 ebt_snat ARP 写越界
- CVE-2026-23111 nftables catchall genmask 反转
- hmbird.h 序列点 UB 修复

stable 8 项（commit `fd8d7cb3a`）：
- af_unix UAF tail->len（be309f8eae8b）
- fs/buffer bh_read UAF（7375f22495e7）
- ext4 hole length 溢出（02c7f7219ac0）
- ebtables compat_mtw OOB（f438d1786d65）
- ipv6 mcast MLD UAF（791c91dc7a9d）
- ctnetlink refcount 泄漏（de788b2e6227）
- blk-cgroup rstat flush UAF（0ab5ee5a1bad）
- xfrm policy inexact bin UAF（7f2d76c9c032）

f2fs 7 项（commit `619b50381`）：
- f2fs write_end_io UAF node_inode（2d9c4a4ed4ee）
- f2fs get_dnode_of_data OOB（77de19b6867f）
- f2fs corrupted nid 检测（8fc6056dcf7）
- f2fs __destroy_extent_node bug_on 删除（1f70ddb2）
- f2fs fiemap 边界处理（95e159ad3e52）
- f2fs discard_cmd_cnt 竞态（6af249c996f）
- f2fs multidev trace 修复（eb2ca3ca9835）
- f2fs pin file offset rounddown（4275b59673e）

virt/geniezone 3 项（commit `c899081ec`，backport 自 mt6993）：
- vcpu 生命周期 UAF（kref 引用计数）
- ioeventfd 销毁泄漏
- irqfd SRCU 泄漏

hmbird 6 项：
- set_audio_thread_sched_prop RCU UAF
- MT6991 集群拓扑 cpu6 partial→big
- hmbird_ops_disabling stub 补全
- cgroup_put(NULL) 防护
- heartbeat 超时 2.5s→10s
- see=NULL 死代码清理

### 性能

- ZRAM =m → =y（内置进 vmlinux）

### 功能增强

- SECURITY_YAMA（ptrace 限制）
- BTRFS_FS + BTRFS_FS_POSIX_ACL
- NTFS3_FS

### 构建流

- AK3 fork + rebrand（Alhkxsj/AnyKernel3）
- release_enable 选项
- spoof_version 一键伪装 /proc/version
- SUSFS try_umount 关闭（droidspaces 兼容）
- kpm 互斥检查
- USER_NS + fix_oplus_bsp_midas ghost-task guard
- SYSVIPC kABI patch 恢复
- CVE-2026-43499 patch 已合入源码，构建流步骤已移除

## 待办

### 1. 安全（最高优先级）

拉取 stable 6.6.y changelog（6.6.90+），逐条核对是否已合，未合的按本仓库风格适配后提交。重点关注 UAF、race、提权类。

### 2. 性能调优（运行时，不改源码）

- hmbird rescue 阈值（parctrl_high_ratio 55→70-75，proc 可调）
- TCP buffer（tcp_rmem/tcp_wmem，sysctl 可调）
- hmbird ravg window（sched_ravg_window_frame_per_sec，proc 可调）

### 3. 上游同步

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
