ZRAM Config
===========

Configures ZRAM compressed swap for better memory management on Linux systems. ZRAM creates a compressed block device in RAM that can be used as swap space, improving performance and reducing wear on storage devices.

Requirements
------------

- Ansible 2.12 or later
- Root/sudo privileges for package installation and system configuration
- Modern Linux distribution with systemd support

**Testing Status**: Designed for systemd-based Linux distributions with ZRAM support.

Role Variables
--------------

The following variables can be configured in `defaults/main.yml`:

- `zram_config_enable: true` - Whether to enable ZRAM configuration
- `zram_config_install_packages: true` - Whether to install ZRAM packages if not present
- `zram_config_packages` - List of packages to install (default: zram)
- `zram_config_file_path: "/etc/systemd/zram-generator.conf"` - Path to ZRAM generator config file
- `zram_config_device_name: "zram0"` - Name of the ZRAM device to configure
- `zram_config_fraction: 0.8` - Fraction of system RAM to use for ZRAM (0.0-1.0)
- `zram_config_max_size_mb: 13107` - Maximum size in MB for ZRAM device (~13GB)
- `zram_config_restart_service: true` - Whether to restart the ZRAM service after configuration
- `zram_config_service_name` - Name of the systemd service for ZRAM setup (auto-generated)
- `zram_config_configure_sysctl: true` - Whether to configure related sysctl parameters
- `zram_config_oom_kill_allocating_task: 1` - Make OOM killer more aggressive (kill allocating task)
- `zram_config_vfs_cache_pressure: 500` - Aggressively reclaim filesystem metadata cache
- `zram_config_swappiness: 100` - How aggressively kernel swaps to swap space (0-200)

Dependencies
------------

None.

Example Playbook
----------------

```yaml
- hosts: workstations
  become: true
  roles:
    - role: zram_config
```

Or with custom configuration:

```yaml
- hosts: workstations
  become: true
  roles:
    - role: zram_config
      zram_config_fraction: 0.5
      zram_config_max_size_mb: 8192
      zram_config_vfs_cache_pressure: 200
```

What This Role Does
-------------------

1. **Install Packages**: Ensures zram package is installed
2. **Check Configuration**: Reads existing ZRAM configuration if present
3. **Configure ZRAM Device**: Sets up ZRAM device in `/etc/systemd/zram-generator.conf`
4. **Configure Sysctl**: Sets up memory management parameters in `/etc/sysctl.d/99-memory-management.conf`
5. **Update Config**: Uses proper Ansible modules for idempotent configuration
6. **Restart Service**: Restarts systemd-zram-setup service only if configuration changed
7. **Feedback**: Provides clear messages about configuration status

**Configuration Files (Following Linux Best Practices):**

**`/etc/systemd/zram-generator.conf`** (ZRAM device settings):
- `[zram0]` section with device-specific settings
- `zram-fraction` and `max-zram-size` parameters

**`/etc/sysctl.d/99-memory-management.conf`** (memory management parameters):
- `vm.oom_kill_allocating_task=1`: Makes OOM killer target the allocating process
- `vm.vfs_cache_pressure=500`: Aggressively reclaims filesystem metadata cache
- `vm.swappiness=100`: Aggressively uses swap space (optimal for fast ZRAM)

**Benefits of ZRAM:**
- Reduces disk I/O by using compressed RAM for swap
- Improves system responsiveness under memory pressure
- Extends SSD lifespan by reducing swap writes
- Particularly beneficial for systems with limited RAM

**Why Swappiness=100 for ZRAM:**

ZRAM is a compressed RAM-based block device, so swapping to ZRAM is much faster than swapping to a traditional disk or SSD.

**Recommended value:** 100 (or close to it, like 90-100)

Since ZRAM is in RAM and uses compression, there's little downside to swapping early. This allows the system to keep more processes in memory, just compressed. A value of 100 ensures the kernel uses ZRAM as much as possible, which is ideal for performance and memory efficiency.

License
-------

GPL-3.0-only

Author Information
------------------

Guillaume Andre (@guiand888)
