# packages

An Ansible role to install a specified list of packages on your workstation.

## Requirements

This role is designed for and tested on Fedora Linux distributions only.

## Role Variables

**Example:**

```yaml
fedora_package_installer_packages_to_install:
  - htop
  - hugo
  - git
```

## Example Playbook

To use this role, define the `fedora_package_installer_packages_to_install` variable in your playbook or inventory.

```yaml
---
- name: Install common development packages on Fedora
  hosts: your_fedora_servers
  become: true # Required for package installation

  vars:
    # Define the list of packages to install
    fedora_package_installer_packages_to_install:
      - htop
      - hugo
      - git

  roles:
    - packages
```

## License

GPL-3.0-or-later

## Author

Guillaume A. (guiand888)
