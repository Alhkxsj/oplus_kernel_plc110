# oplus_kernel_plc110

一加 Ace 5 至尊版 (PLC110) 天玑 9400+ 6.6.89 (MT6991) 内核构建仓库。

## 支持机型

- 一加 Ace 5 至尊版 (PLC110) — 天玑 9400+ (MT6991)

## 功能

- 基于一加官方 6.6.89 OKI 内核源码
- 风驰 (hmbird) 调度器
- BakaSU / SukiSU / KernelSU Next / 原版 KSU / 无 KSU
- SUSFS 隐藏
- lz4 1.10.0 + zstd 1.5.7
- ADIOS IO 调度器、Re-Kernel、BBR / Brutal
- Droidspaces 容器（standard / extend）
- Baseband-guard 基带保护
- /proc/version 一键伪装
- Release 可选发布

## 安全修复

### CVE（4 项）

- CVE-2026-43499 rtmutex waiter::task UAF
- CVE-2026-46242 epoll ep_remove UAF
- CVE-2026-53266 ebt_snat ARP 写越界
- CVE-2026-23111 nftables catchall genmask 反转

### stable backport（8 项）

- af_unix UAF tail->len
- fs/buffer bh_read UAF
- ext4 hole length 整数溢出
- ebtables compat_mtw OOB read
- ipv6 mcast MLD query UAF
- ctnetlink refcount 泄漏
- blk-cgroup rstat flush UAF
- xfrm policy inexact bin UAF

### f2fs（7 项）

- write_end_io UAF node_inode
- get_dnode_of_data OOB
- corrupted nid 检测
- __destroy_extent_node bug_on 删除
- fiemap 边界处理
- discard_cmd_cnt 竞态
- multidev trace
- pin file offset rounddown

### virt/geniezone（3 项，backport 自 mt6993）

- vcpu 生命周期 UAF：kref 引用计数，vcpu create 时 get、release 时 put，vm 在最后一个引用释放时 destroy
- ioeventfd 销毁泄漏：gzvm_destroy_vm 补 ioeventfd release
- irqfd SRCU 泄漏：补 cleanup_srcu_struct

> 保留 6.6.89 API，未引入 fd_file / eventfd_signal 单参数 / remove void 适配。

### hmbird（6 项）

- set_audio_thread_sched_prop RCU UAF
- MT6991 集群拓扑 cpu6 partial→big
- hmbird_ops_disabling stub 补全
- cgroup_put(NULL) 防护
- heartbeat 超时 2.5s→10s
- see=NULL 死代码清理

## 编译

GitHub Actions：Actions → Run workflow。

本地：`local/builder_6.6.89_mtk.sh`

## 内核源码

[Alhkxsj/android_kernel_oneplus_mt6991](https://github.com/Alhkxsj/android_kernel_oneplus_mt6991) — 分支 `oneplus/mt6991_v_15.0.2_ace5_ultra_6.6.89`

## 鸣谢

- [cctv18/oppo_oplus_realme_sm8750](https://github.com/cctv18/oppo_oplus_realme_sm8750) — 构建脚本
- [OnePlusOSS](https://github.com/OnePlusOSS/android_kernel_oneplus_mt6991) — 内核源码
- [BakaSU](https://github.com/Baka-SU/BakaSU)
- [SukiSU Ultra](https://github.com/SukiSU-Ultra/SukiSU-Ultra)
- [KernelSU Next](https://github.com/pershoot/KernelSU-Next)
- [KernelSU](https://github.com/tiann/KernelSU)
- [Alhkxsj/AnyKernel3](https://github.com/Alhkxsj/AnyKernel3)
