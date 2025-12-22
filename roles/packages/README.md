# packages

An Ansible role to install a specified list of packages on your workstation with individual package progress display.

## Requirements

This role is designed for and tested on Fedora Linux distributions only.

## Role Variables

**Example:**

```yaml
packages_packages_to_install:
  - htop
  - hugo
  - git
```

## Features

- **Individual Package Progress**: Each package installation is displayed separately, showing which package is currently being installed.
- **Loop-based Installation**: Uses Ansible loops to process packages one by one for better visibility.
- **Clear Labels**: Shows package names as labels during installation for easy tracking.

## Example Playbook

To use this role, define the `packages_packages_to_install` variable in your playbook or inventory.

```yaml
---
- name: Install common development packages on Fedora
  hosts: your_fedora_servers
  become: true # Required for package installation

  vars:
    # Define the list of packages to install
    packages_packages_to_install:
      - htop
      - hugo
      - git

  roles:
    - packages
```

## Usage Notes

- The role now shows progress for each individual package during installation
- Package names are displayed as labels in the Ansible output
- Empty package lists are handled gracefully (no installation attempted)

## License

GPL-3.0-or-later

## Author

Guillaume A. (guiand888)
