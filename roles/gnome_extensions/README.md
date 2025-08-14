# gnome_extensions

An Ansible role to install GNOME Shell extensions via `dnf` on Fedora systems.

## Requirements

* This role is designed for and tested on Fedora Linux distributions.
* Assumes `dnf` is the package manager.
* Assumes the GNOME Shell extensions are available as packages following the `gnome-shell-extension-` naming convention.

## Role Variables

* `gnome_extensions_extensions_to_install` (list of strings)
  A list of base GNOME Shell extension names. The role will automatically prefix these names with `gnome-shell-extension-` to form the DNF package name.

  * Default: `[]` (an empty list)

  * Example:

```yaml
gnome_extensions_extensions_to_install:
  - places-menu
  - dash-to-panel
  - clipboard-indicator
  - blur-my-shell
```

## Example Playbook

To use this role, define the `gnome_extensions_extensions_to_install` variable in your playbook or inventory.

```yaml
---
- name: Install common GNOME Shell extensions
  hosts: your_fedora_workstations
  become: true # Required for dnf package installation

  vars:
    # Define the list of GNOME Shell extensions to install
    gnome_extensions_extensions_to_install:
      - places-menu
      - dash-to-panel
      - appindicator
      - user-theme
      - Vitals
      - system-monitor-next

  roles:
    - gnome_extensions
```

## License

GPL-3.0-or-later

## Author

Guillaume A. (guiand888)
