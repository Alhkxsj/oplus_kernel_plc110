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
- CVE-2026-43499 (GhostLock) rtmutex 修复
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
