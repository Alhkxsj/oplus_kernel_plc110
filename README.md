# oplus_kernel_plc110

一加 Ace 5 至尊版 (PLC110) 天玑 9400+ 6.6.89 内核编译脚本

> 基于 [cctv18/oppo_oplus_realme_sm8750](https://github.com/cctv18/oppo_oplus_realme_sm8750) 项目

## 支持机型
- 一加 Ace 5 至尊版 (PLC110) - 天玑 9400+ (MT6991)

## 功能特性
- OKI 官方内核编译（基于一加 Ace5 至尊版 6.6.89 源码）
- 风驰 scx 调速器移植
- 多版本 KSU 可选（ReSukiSU/SukiSU Ultra/KernelSU Next）
- ccache-ECS 缓存优化，编译时间约 6min
- O2 编译优化
- lz4/zstd 算法更新补丁
- BBR/Brutal TCP 拥塞控制
- Droidspaces 容器化支持
- 内核防格基带保护

## 编译方式
- GitHub Action 在线编译
- Shell 本地编译（`local/builder_6.6.89_mtk.sh`）

## 鸣谢
- [cctv18/oppo_oplus_realme_sm8750](https://github.com/cctv18/oppo_oplus_realme_sm8750) - 原始项目
- [ReSukiSU](https://github.com/ReSukiSU/ReSukiSU)
- [SukiSU Ultra](https://github.com/SukiSU-Ultra/SukiSU-Ultra)
- [KernelSU Next](https://github.com/pershoot/KernelSU-Next)
