# zswap_config

Configures zswap (compressed swap cache in front of a real swap device) on Fedora and openSUSE Tumbleweed, and turns swap-on-zram off if it is present (Fedora enables it by default).

## Requirements

- Fedora or openSUSE Tumbleweed (`os_family` RedHat or Suse); other systems are skipped with a message.
- Root privileges.
- On btrfs, `btrfs-progs` (installed by the role when it has to create the swapfile).

## What it does, in order

1. **Backing swap**: zswap does nothing without a swap device. If no non-zram swap is active, a swapfile is created (btrfs: dedicated `@swap` subvolume + `btrfs filesystem mkswapfile`; other filesystems: `fallocate` + `mkswap`), registered in `/etc/fstab` and activated. With `zswap_config_swapfile_enable: false` and no disk swap, the role fails before touching zram.
2. **Disable zram** (`zswap_config_disable_zram`): replaces an active `/etc/systemd/zram-generator.conf` with a comment-only file (the documented override), stops `zramswap.service` on openSUSE, swaps off and resets any lingering `/dev/zram*`. Packages are not removed.
3. **Kernel command line** (`zswap_config_manage_kernel_cmdline`): rewrites every `zswap.*` argument and leaves the rest alone.
   - Fedora: `grubby --update-kernel=ALL`.
   - openSUSE: depends on `LOADER_TYPE` in `/etc/sysconfig/bootloader`: `grub2`/`grub2-efi` edit `GRUB_CMDLINE_LINUX_DEFAULT` and run `update-bootloader --refresh`; `grub2-bls`/`systemd-boot` edit `/etc/kernel/cmdline` and run `sdbootutil update-all-entries`. Any other loader fails with an explanatory message. **The openSUSE refresh commands have not been verified against a live Tumbleweed install.**
4. **sysctl**: `vm.swappiness` and `vm.page-cluster` in `/etc/sysctl.d/99-zswap.conf`.
5. **Runtime**: writes the parameters to `/sys/module/zswap/parameters/*` so a reboot is not needed. Reboot anyway to confirm the boot-time values.

## Role Variables

See `defaults/main.yml` for the full documented list. Defaults mirror the reference workstation:

| Variable | Default |
| --- | --- |
| `zswap_config_enabled` | `true` |
| `zswap_config_compressor` | `zstd` |
| `zswap_config_max_pool_percent` | `20` |
| `zswap_config_accept_threshold_percent` | `90` |
| `zswap_config_shrinker_enabled` | `true` |
| `zswap_config_zpool` | `""` (parameter removed on recent kernels; set e.g. `z3fold` for older ones) |
| `zswap_config_swappiness` | `10` |
| `zswap_config_page_cluster` | `0` (kernel default is 3) |
| `zswap_config_swapfile_enable` | `true` |
| `zswap_config_swapfile_path` / `_size` / `_priority` | `/swap/swapfile` / `32G` / `0` |
| `zswap_config_swap_btrfs_subvolume` | `@swap` |

## Example Playbook

```yaml
- name: Configure zswap
  hosts: workstations
  become: true
  vars:
    zswap_config_max_pool_percent: 25
    zswap_config_swapfile_size: 16G
  roles:
    - guiand888.workstation_bootstrap.zswap_config
```

## Notes

- This role and `zram_config` both manage `/etc/systemd/zram-generator.conf`; do not run both on the same host.
- Check mode: the `grubby`/`swapon` steps are command tasks and are skipped, not reported as changed.

## License

GPL-3.0-or-later

## Author

Guillaume Andre (@guiand888)
