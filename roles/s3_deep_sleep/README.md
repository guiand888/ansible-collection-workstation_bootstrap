S3 Deep Sleep
=============

Configures S3 deep sleep mode for better power management on Linux laptops. This role enables deeper sleep states that can significantly improve battery life during suspend.

Requirements
------------

- Ansible 2.12 or later
- Linux system with sysfs power management support
- GRUB bootloader (for GRUB configuration updates)
- Root/sudo privileges

**Testing Status**: Designed for laptops supporting S3 sleep states. Tested on Dell XPS systems with Fedora.

Role Variables
--------------

The following variables can be configured in `defaults/main.yml`:

- `s3_deep_sleep_enable: true` - Whether to enable S3 deep sleep mode
- `s3_deep_sleep_mem_sleep_path: "/sys/power/mem_sleep"` - Path to the mem_sleep sysfs file
- `s3_deep_sleep_mode: "deep"` - Sleep mode to configure ('deep', 's2idle', 'shallow')
- `s3_deep_sleep_update_grub: true` - Whether to update GRUB configuration after change
- `s3_deep_sleep_grub_config_path: "/boot/efi/EFI/fedora/grub.cfg"` - Path to GRUB config file
- `s3_deep_sleep_check_current: true` - Whether to check current setting before applying

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
      s3_deep_sleep_grub_config_path: "/boot/grub2/grub.cfg"
```

What This Role Does
-------------------

1. **Check Support**: Verifies that the system supports sleep mode configuration
2. **Read Current**: Reads the current sleep mode setting
3. **Compare**: Only makes changes if the current setting differs from desired
4. **Configure**: Sets the sleep mode via sysfs (/sys/power/mem_sleep)
5. **Update GRUB**: Regenerates GRUB configuration to persist the setting

**Important Notes:**
- This role is particularly beneficial for Dell XPS and similar laptops
- The 'deep' sleep mode provides better power savings than 's2idle'
- Changes take effect immediately and persist across reboots

License
-------

GPL-3.0-only

Author Information
------------------

Guillaume Andre (@guiand888)
