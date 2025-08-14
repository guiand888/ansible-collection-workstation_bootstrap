DNF Fastest Mirror
==================

Configures DNF to use the fastest mirror functionality for improved package download speeds on Fedora systems.

Requirements
------------

- Ansible 2.12 or later
- Fedora Linux with DNF package manager
- Root/sudo privileges for configuration file modification

**Testing Status**: Designed specifically for Fedora systems with DNF.

Role Variables
--------------

The following variables can be configured in `defaults/main.yml`:

- `dnf_fastestmirror_enable: true` - Whether to enable fastest mirror configuration
- `dnf_fastestmirror_config_file: "/etc/dnf/dnf.conf"` - Path to DNF configuration file
- `dnf_fastestmirror_value: "1"` - Value to set ("1"=enabled, "0"=disabled)

Dependencies
------------

None.

Example Playbook
----------------

```yaml
- hosts: fedora_systems
  become: true
  roles:
    - role: dnf_fastestmirror
```

Or to disable fastest mirror:

```yaml
- hosts: fedora_systems
  become: true
  roles:
    - role: dnf_fastestmirror
      dnf_fastestmirror_value: "0"
```

What This Role Does
-------------------

1. **Check Configuration**: Verifies DNF configuration file exists
2. **Enable Fastest Mirror**: Sets `fastestmirror=1` in `/etc/dnf/dnf.conf`
3. **Graceful Handling**: Skips configuration if DNF config file doesn't exist
4. **Idempotent**: Only makes changes if current setting is different

**Benefits:**
- Faster package downloads by automatically selecting the fastest mirror
- Improved system update performance
- Reduced bandwidth usage by choosing geographically closer mirrors
- Better user experience during package installations

License
-------

GPL-3.0-only

Author Information
------------------

Guillaume Andre (@guiand888)
