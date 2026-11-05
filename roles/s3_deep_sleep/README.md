S3 Deep Sleep
=============

Configures S3 deep sleep mode for better power management on Linux laptops. This role enables deeper sleep states that can significantly improve battery life during suspend.

Requirements
------------

- Ansible 2.12 or later
- Linux system with sysfs power management support
- Fedora (grubby) or openSUSE Tumbleweed (systemd-boot, GRUB2-BLS or GRUB2)
- Root/sudo privileges

**Testing Status**: Designed for laptops supporting S3 sleep states. Designed for the Dell XPS 13 9300 on openSUSE Tumbleweed (systemd-boot) and Fedora; the Fedora path follows `zswap_config`.

Role Variables
--------------

The following variables can be configured in `defaults/main.yml`:

- `s3_deep_sleep_enable: true` - Whether to enable S3 deep sleep mode
- `s3_deep_sleep_mode: "deep"` - Sleep mode to configure (`deep`, `s2idle` or `shallow`, the values accepted by `mem_sleep_default=`)
- `s3_deep_sleep_manage_kernel_cmdline: true` - Persist `mem_sleep_default=<mode>` on the kernel command line
- `s3_deep_sleep_apply_runtime: true` - Also write the mode to sysfs so it applies without a reboot
- `s3_deep_sleep_mem_sleep_path: "/sys/power/mem_sleep"` - Path to the mem_sleep sysfs file
- `s3_deep_sleep_check_current: true` - Display the current and available modes before applying

Removed in 4.0.0: `s3_deep_sleep_update_grub` and `s3_deep_sleep_grub_config_path` (the role no longer runs `grub2-mkconfig`).

Dependencies
------------

None.

Example Playbook
----------------

```yaml
- hosts: laptops
  become: true
  roles:
    - role: s3_deep_sleep
```

Or with custom configuration:

```yaml
- hosts: laptops
  become: true
  roles:
    - role: s3_deep_sleep
      s3_deep_sleep_mode: "deep"
```

What This Role Does
-------------------

1. **Persist** (`s3_deep_sleep_manage_kernel_cmdline`): sets `mem_sleep_default=<mode>` on the kernel command line, replacing any previous value and leaving other arguments alone:
   - **Fedora**: `grubby --update-kernel=ALL --args=...` (rewrites the BLS entries and the defaults for future kernels).
   - **openSUSE with systemd-boot or GRUB2-BLS**: rewrites `/etc/kernel/cmdline` and runs `sdbootutil update-all-entries`.
   - **openSUSE with classic GRUB2**: edits `GRUB_CMDLINE_LINUX_DEFAULT` in `/etc/default/grub` and runs `update-bootloader --refresh`.
   - The bootloader is read from `LOADER_TYPE` in `/etc/sysconfig/bootloader`; an unknown one fails with instructions.
2. **Apply now** (`s3_deep_sleep_apply_runtime`): writes the mode to `/sys/power/mem_sleep` when the firmware offers it, otherwise warns and skips.

**Important Notes:**
- The 'deep' sleep mode provides better power savings than 's2idle' but only works if the firmware exposes S3.
- Some machines expose it through a BIOS setting (e.g. Dell "Sleep mode"); the kernel argument is then a safeguard.
- A reboot is needed to validate the boot-time setting: check `/proc/cmdline` and `cat /sys/power/mem_sleep`.

License
-------

GPL-3.0-only

Author Information
------------------

Guillaume Andre (@guiand888)
