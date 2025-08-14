# Ansible Collection: guiand888.workstation_bootstrap

Ansible collection for bootstrapping Fedora workstations with system optimizations, package management, and hardware support.

## Installation

```bash
ansible-galaxy collection install guiand888.workstation_bootstrap
```

## Requirements

- Ansible >= 2.9
- Fedora Linux target system
- sudo privileges

## Roles

### System Configuration
- `zram_config` - Configure ZRAM compression
- `dnf_fastestmirror` - Enable DNF fastest mirror
- `hostname_config` - Set system hostname
- `timezone_config` - Configure system timezone
- `keyboard_config` - Set keyboard layout

### Hardware Support
- `s3_deep_sleep` - Enable S3 deep sleep on Dell XPS
- `lid_behavior` - Configure laptop lid behavior
- `systemd_lid_config` - Systemd lid switch configuration
- `trezor_udev` - Trezor hardware wallet udev rules
- `ledger_udev` - Ledger hardware wallet udev rules

### Package Management
- `packages` - Install system packages via DNF
- `gnome_extensions` - Install GNOME Shell extensions
- `ffmpeg_full` - Switch to full FFmpeg with codecs

### Flatpak Applications
- `flatpacks` - Install Flatpak applications
- `flatpak_settings` - Configure Flatpak settings
- `firefox_flatpak` - Replace Firefox RPM with Flatpak

### Applications
- `appimage_daemon` - Configure AppImage daemon
- `aws_cli` - Install AWS CLI v2
- `nextcloud_client` - Configure Nextcloud client

### Service Management
- `service_management` - Manage systemd services

## Usage

```yaml
- name: Configure workstation
  hosts: localhost
  become: true
  roles:
    - guiand888.workstation_bootstrap.zram_config
    - guiand888.workstation_bootstrap.packages
    - guiand888.workstation_bootstrap.flatpacks
```

## Variables

Each role includes comprehensive default variables. Override in `host_vars` or `group_vars`:

```yaml
# Example overrides
zram_config_fraction: 0.75
packages_packages_to_install:
  - htop
  - hugo
flatpacks_packages_to_install:
  - name: org.gimp.GIMP
    method: user
```

## License

GPL-3.0-or-later

## Author

Guillaume Andre (@guiand888)
