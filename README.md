# oplus_kernel_plc110

一加 Ace 5 至尊版 (PLC110) 天玑 9400+ 6.6.89 (MT6991) 内核构建仓库。

## 支持机型

- 一加 Ace 5 至尊版 (PLC110) — 天玑 9400+ (MT6991)

## 功能

- 基于一加官方 6.6.89 OKI 内核源码
- 内置风驰 (hmbird) 调度器（含修复）
- BakaSU / SukiSU / KernelSU Next / 原版 KSU / 无 KSU 五选一
- SUSFS 隐藏（与 Droidspaces 容器兼容，try_umount 冲突已解决）
- lz4 1.10.0 + zstd 1.5.7 算法更新
- ADIOS IO 调度器、Re-Kernel、BBR / Brutal
- Droidspaces 容器（standard / extend）
- Baseband-guard 基带保护

### 安全修复（共 22 项，均已开机验证）

**CVE 修复（4 项）**
- CVE-2026-43499 (GhostLock) rtmutex waiter::task 修复
- CVE-2026-46242 epoll ep_remove UAF + ep_free kfree_rcu 修复
- CVE-2026-53266 ebt_snat ARP SHA 写越界修复
- CVE-2026-23111 nftables catchall genmask 反转修复

**通用 stable 修复（8 项）**
- af_unix UAF tail->len 修复
- fs/buffer bh_read UAF 修复
- ext4 hole length 整数溢出修复
- ebtables compat_mtw OOB read 修复
- ipv6 mcast MLD query UAF 修复
- ctnetlink refcount 泄漏修复
- blk-cgroup rstat flush UAF 修复
- xfrm policy inexact bin UAF 修复

**f2fs 修复（7 项）**
- f2fs write_end_io UAF node_inode 修复
- f2fs get_dnode_of_data OOB 修复
- f2fs corrupted nid 检测
- f2fs __destroy_extent_node bug_on 删除
- f2fs fiemap 边界处理
- f2fs discard_cmd_cnt 竞态修复
- f2fs multidev trace 修复
- f2fs pin file offset rounddown 修复

**virt/geniezone 修复（3 项）**
- vcpu 生命周期 UAF（kref 引用计数）
- ioeventfd 销毁未清理（内存泄漏）
- irqfd SRCU 结构未清理

**其他**
- hmbird.h 序列点 UB 修复
- /proc/version 一键伪装（spoof_version）
- ccache 缓存，O2 优化

## 编译

GitHub Actions：仓库 Actions → Run workflow，按选项填写。

本地：`local/builder_6.6.89_mtk.sh`

刷入：AnyKernel3 刷机包，TWRP / HorizonKernelFlasher / KSU 管理器均可。

## 内核源码

[Alhkxsj/android_kernel_oneplus_mt6991](https://github.com/Alhkxsj/android_kernel_oneplus_mt6991) — 分支 `oneplus/mt6991_v_15.0.2_ace5_ultra_6.6.89`

## 鸣谢

- [cctv18/oppo_oplus_realme_sm8750](https://github.com/cctv18/oppo_oplus_realme_sm8750) — 原始构建脚本
- [OnePlusOSS](https://github.com/OnePlusOSS/android_kernel_oneplus_mt6991) — 内核源码
- [BakaSU](https://github.com/Baka-SU/BakaSU)
- [SukiSU Ultra](https://github.com/SukiSU-Ultra/SukiSU-Ultra)
- [KernelSU Next](https://github.com/pershoot/KernelSU-Next)
- [KernelSU](https://github.com/tiann/KernelSU)
