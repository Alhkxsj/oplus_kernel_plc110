# oplus_kernel_plc110

OnePlus Ace 5 Ultra (PLC110) Dimensity 9400+ 6.6.89 (MT6991) kernel build.

## Device

- OnePlus Ace 5 Ultra (PLC110) — Dimensity 9400+ (MT6991)

## Features

- Based on OnePlus 6.6.89 OKI kernel source
- Hmbird scheduler (with fixes)
- BakaSU / SukiSU / KernelSU Next / KSU / None
- SUSFS
- lz4 1.10.0 + zstd 1.5.7
- ADIOS IO scheduler, Re-Kernel, BBR / Brutal
- Droidspaces container (standard / extend)
- Baseband-guard
- /proc/version spoof
- Optional release publishing
- AnyKernel3 (own fork)

## Kernel fixes (28 files, 252 insertions)

### CVE (4)

- CVE-2026-43499 rtmutex: `remove_waiter()` uses `waiter->task` instead of `current`; `rt_mutex_start_proxy_lock()` return check `ret < 0`; null waiter guard
- CVE-2026-46242 epoll: `__ep_remove()` pins `@file` via `epi_fget()` before f_lock; `ep_free()` uses `kfree_rcu`; `WRITE_ONCE(epi->dying)` paired with reader
- CVE-2026-53266 ebt_snat: `skb_ensure_writable()` before ARP SHA rewrite
- CVE-2026-23111 nftables: `nft_map_catchall_deactivate()` genmask check fix

### stable backport (8)

- af_unix: remove `tail->len` compare in `unix_stream_data_wait()` (be309f8eae8b)
- buffer: `put_bh` moved before `__end_buffer_read_notouch()` (7375f22495e7)
- ext4: `ext4_ind_map_blocks()` count→u64, m_len uses umin (02c7f7219ac0)
- ebtables: `compat_mtw_from_user()` size validation (f438d1786d65)
- ipv6 mcast: `__mld_query_work()` group value copy (791c91dc7a9d)
- ctnetlink: refcount leak fix (de788b2e6227)
- blk-cgroup: rstat flush UAF fix
- xfrm: inexact bin UAF fix

### f2fs (7)

- `f2fs_write_end_io`: `f2fs_in_warm_node_list` before `dec_page_count` (2d9c4a4ed4ee)
- `f2fs_get_dnode_of_data`: `nid == i_ino` validation (77de19b6867f)
- `f2fs_alloc_nid`: `is_invalid_nid` check + `STOP_CP_REASON_CORRUPTED_NID` (8fc6056dcf7)
- `__destroy_extent_node`: remove `f2fs_bug_on(node_cnt)`
- `f2fs_map_blocks`: fiemap bounds fix (95e159ad3e52)
- `f2fs/segment`: discard_cmd_cnt race fix
- `f2fs/file`: pin file offset rounddown fix

### virt/geniezone (3, backport from mt6993)

- vcpu lifecycle UAF: kref refcount, vcpu create→get, release→put, vm destroy on last put
- ioeventfd: `gzvm_vm_ioeventfd_release()` added to `gzvm_destroy_vm`
- irqfd: `cleanup_srcu_struct()` added to `gzvm_vm_irqfd_release`

### hmbird (6)

- `set_audio_thread_sched_prop`: RCU UAF — `strcmp` moved into `rcu_read_lock` critical section
- MT6991 topology: cpu6 `partial`→`big` (4×A520 + 3×A725 + 1×X925)
- `hmbird_ops_disabling()`: stub `return false` → real state check
- `init_child_tg`: `cgroup_put(NULL)` guard
- heartbeat timeout: 2500ms→10000ms
- `see = NULL` dead code removal

## Build

GitHub Actions: Actions → Run workflow.

Local: `local/builder_6.6.89_mtk.sh`

## Source

[Alhkxsj/android_kernel_oneplus_mt6991](https://github.com/Alhkxsj/android_kernel_oneplus_mt6991) — `oneplus/mt6991_v_15.0.2_ace5_ultra_6.6.89`

## Credits

- [cctv18/oppo_oplus_realme_sm8750](https://github.com/cctv18/oppo_oplus_realme_sm8750)
- [OnePlusOSS](https://github.com/OnePlusOSS/android_kernel_oneplus_mt6991)
- [BakaSU](https://github.com/Baka-SU/BakaSU)
- [SukiSU Ultra](https://github.com/SukiSU-Ultra/SukiSU-Ultra)
- [KernelSU Next](https://github.com/pershoot/KernelSU-Next)
- [KernelSU](https://github.com/tiann/KernelSU)
- [Alhkxsj/AnyKernel3](https://github.com/Alhkxsj/AnyKernel3)
