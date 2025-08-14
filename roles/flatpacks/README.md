# flatpacks

An Ansible role to install Flatpak applications on a system.

## Requirements

This role requires the `flatpak` package and its environment to be set up on the target system. This role does not install Flatpak itself.
This role requires the `community.general` collection to be installed (`ansible-galaxy collection install community.general`).

## Role Variables

* `flatpacks_packages_to_install` (list of dictionaries)
  A list of Flatpak applications to install. Each dictionary should have:
  * `name` (string, **required**): The Flatpak application ID (e.g., `org.gimp.GIMP`).
  * `method` (string, *optional*): Specifies how the Flatpak should be installed. Accepted values are `user` (for the current user) or `system` (system-wide). If not specified for an item, it defaults to `flatpacks_install_method_default`.

**Example:**

```yaml
flatpacks_packages_to_install:
  - name: org.gimp.GIMP
    method: system # Install GIMP system-wide
  - name: org.inkscape.Inkscape # Will use the default method
  - name: org.kde.kdenlive
    method: user # Explicitly install Kdenlive for the current user
```

* `flatpacks_install_method_default` (string)
  The default installation method for Flatpaks, applied when `method` is not specified for an individual item in `flatpacks_packages_to_install`.
  * Default: `"user"`

## Example Playbook

To use this role, define the `flatpacks_packages_to_install` variable in your playbook or inventory.

```yaml
---
- name: Install Flatpak applications
  hosts: all
  become: true # Needed for system-wide flatpaks, or if managing user homes directly

  vars:
    flatpacks_install_method_default: "user" # Explicitly set default for this playbook

    # Define the list of Flatpak apps to install
    flatpacks_packages_to_install:
      - name: org.gimp.GIMP
        method: system
      - name: org.mozilla.firefox
      - name: com.spotify.Client
      - name: org.libreoffice.LibreOffice
        method: system
      - name: org.videolan.VLC

  roles:
    - flatpacks
```

## License

GPL-3.0-or-later

## Author

Guillaume A. (guiand888)
