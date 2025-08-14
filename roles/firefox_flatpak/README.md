Firefox Flatpak
===============

Replaces the Firefox RPM package with the Firefox Flatpak version on Linux systems. Includes intelligent profile migration from the RPM Firefox location (`~/.mozilla/firefox`) to the Flatpak Firefox location (`~/.var/app/org.mozilla.firefox/.mozilla/firefox`).

Requirements
------------

- Ansible 2.12 or later
- Linux system with Flatpak support
- `community.general` collection for Flatpak module
- `ansible.posix` collection for synchronize module

**Testing Status**: Tested on Fedora 42, but should work on other Linux distributions with Flatpak support.

Role Variables
--------------

The following variables can be configured in `defaults/main.yml`:

- `firefox_flatpak_remove_rpm: true` - Whether to remove the Firefox RPM package
- `firefox_flatpak_install_flatpak: true` - Whether to install the Firefox Flatpak
- `firefox_flatpak_install_method: "user"` - Installation method ('user' or 'system')
- `firefox_flatpak_package_name: "org.mozilla.firefox"` - The Flatpak package name
- `firefox_flatpak_migrate_profile: true` - Whether to migrate existing profiles from RPM to Flatpak location
- `firefox_flatpak_rpm_profile_path` - Path to RPM Firefox profiles (default: `~/.mozilla/firefox`)
- `firefox_flatpak_flatpak_profile_path` - Path to Flatpak Firefox profiles (default: `~/.var/app/org.mozilla.firefox/.mozilla/firefox`)

Dependencies
------------

- `community.general` collection (for Flatpak module)
- `ansible.posix` collection (for synchronize module)

Example Playbook
----------------

```yaml
- hosts: workstations
  become: true
  roles:
    - role: firefox_flatpak
      firefox_flatpak_install_method: "user"
```

License
-------

GPL-3.0-only

Author Information
------------------

Guillaume Andre (@guiand888)
