# Ansible Collection: guiand888.workstation_bootstrap

Ansible collection for bootstrapping Fedora and openSUSE Tumbleweed workstations with system optimizations, package management, and hardware support.

## Installation

```bash
ansible-galaxy collection install guiand888.workstation_bootstrap
```

## Requirements

- Ansible >= 2.9
- Fedora or openSUSE Tumbleweed target system
- sudo privileges

## Roles

### System Configuration
- `zswap_config` - Configure zswap compressed swap (replaces swap-on-zram)
- `dnf_fastestmirror` - Enable DNF fastest mirror
- `hostname_config` - Set system hostname
- `timezone_config` - Configure system timezone
- `keyboard_config` - Set keyboard layout

### Hardware Support
- `s3_deep_sleep` - Enable S3 deep sleep on Dell XPS
- `systemd_lid_config` - Systemd lid switch configuration
- `trezor_udev` - Trezor hardware wallet udev rules
- `ledger_udev` - Ledger hardware wallet udev rules

### Package Management
- `packages` - Install system packages (dnf or zypper)
- `gnome_extensions` - Install GNOME Shell extensions
- `ffmpeg_full` - Switch to full FFmpeg with codecs (Fedora)
- `packman_codecs` - Switch ffmpeg libraries and codec extras to a third-party repository, VideoLAN by default (openSUSE)
  Also installs `pipewire-aptx` from Packman (aptX Bluetooth); Packman ends on 2027-01-01, then set `packman_codecs_aptx: false`.

### Flatpak Applications
- `flatpak` - Install Flatpak applications
- `flatpak_settings` - Configure Flatpak settings
- `firefox_flatpak` - Replace Firefox RPM with Flatpak

### Applications
- `nextcloud_client` - Configure Nextcloud client
- `ansible_vault_bitwarden` - Install Bitwarden CLI and ansible-vault integration

### Developer Tooling
- `nix` - Distro-packaged multi-user Nix daemon (Fedora, openSUSE Tumbleweed)

### Service Management
- `service_management` - Manage systemd services

## Usage

```yaml
- name: Configure workstation
  hosts: localhost
  become: true
  roles:
    - guiand888.workstation_bootstrap.zswap_config
    - guiand888.workstation_bootstrap.packages
    - guiand888.workstation_bootstrap.flatpak
```

## Variables

Each role includes comprehensive default variables. Override in `host_vars` or `group_vars`:

```yaml
# Example overrides
zswap_config_max_pool_percent: 25
packages_packages_to_install:
  - htop
  - hugo
flatpak_packages_to_install:
  - name: org.gimp.GIMP
    method: user
```

## License

GPL-3.0-or-later

## Author

Guillaume Andre (@guiand888)
