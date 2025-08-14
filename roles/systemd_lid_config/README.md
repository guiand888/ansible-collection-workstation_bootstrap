# systemd_lid_config

An Ansible role to configure lid closing behavior in `/etc/systemd/logind.conf`.

## Requirements

This role is intended for Linux systems using `systemd` and specifically relies on the `logind.conf` configuration file.

## Role Variables

* **`systemd_lid_config_default_action`** (string)
    Sets the default action to be taken when the laptop lid is closed, overriding any specific settings if they are not explicitly defined.
    Common values include: `"suspend"`, `"ignore"`, `"poweroff"`, `"hibernate"`, `"kexec"`, `"lock"`.
    **Default:** `"suspend"`

* **`systemd_lid_config_actions`** (dictionary)
    A map defining specific actions for different lid-switch scenarios. These settings take precedence over `systemd_lid_config_default_action` if explicitly set.
    Keys can be `handle_lid_switch`, `handle_lid_switch_external_power`, and `handle_lid_switch_docked`.

**Default:**

```yaml
systemd_lid_config_actions:
  handle_lid_switch: "{{ systemd_lid_config_default_action }}"
  handle_lid_switch_external_power: "{{ systemd_lid_config_default_action }}"
  handle_lid_switch_docked: "{{ systemd_lid_config_default_action }}"
```

**Example (overriding defaults):**

```yaml
systemd_lid_config_actions:
  handle_lid_switch: "suspend"
  handle_lid_switch_external_power: "ignore" # Don't suspend when connected to external power
  handle_lid_switch_docked: "ignore"       # Don't suspend when docked
```

## Example Playbook

To use this role, you can simply include it without specific variable overrides, or customize behavior:

```yaml
---
- name: Apply systemd lid closing configuration
  hosts: my_laptops
  become: true # Required to write to /etc/systemd/logind.conf and manage systemd services

  vars:
    # Example: Override only the docked behavior
    systemd_lid_config_actions:
      handle_lid_switch_docked: "ignore"

    # Example: Set a global default other than suspend
    # systemd_lid_config_default_action: "poweroff"

  roles:
    - systemd_lid_config
```

## License

GPL-3.0-or-later

## Author

Guillaume A. (guiand888)