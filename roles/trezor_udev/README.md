# Trezor Udev Role

Installs official Trezor hardware wallet udev rules for proper device access on Linux systems. Downloads rules directly from Trezor's official source and configures system for Trezor device detection.

## Features

- **Official Rules**: Downloads from `https://data.trezor.io/udev/51-trezor.rules`
- **Automatic Backup**: Backs up existing rules before replacement
- **Udev Integration**: Reloads and triggers udev after installation
- **Multi-Device Support**: Supports both Trezor One and Trezor Model T
- **Idempotent**: Safe to run multiple times
- **Verification**: Confirms successful installation

## Requirements

- Linux system with udev
- Root/sudo privileges for udev rules installation
- Internet connection to download official rules

## Role Variables

### Control Variables

- `trezor_udev_enable` (default: `true`): Whether to install Trezor udev rules
- `trezor_udev_backup_existing` (default: `true`): Backup existing rules before replacement
- `trezor_udev_reload_rules` (default: `true`): Reload udev rules after installation

### Path Configuration

- `trezor_udev_rules_url` (default: `"https://data.trezor.io/udev/51-trezor.rules"`): Official rules URL
- `trezor_udev_rules_path` (default: `"/etc/udev/rules.d/51-trezor.rules"`): Local installation path

## Supported Devices

The udev rules support:
- **Trezor One** (Vendor ID: 534c, Product ID: 0001)
- **Trezor Model T** (Vendor ID: 1209, Product IDs: 53c0, 53c1)

## Example Playbooks

### Basic Usage

```yaml
- hosts: workstations
  become: true
  roles:
    - guiand888.workstation.trezor_udev
```

### Skip Backup

```yaml
- hosts: workstations
  become: true
  vars:
    trezor_udev_backup_existing: false
  roles:
    - guiand888.workstation.trezor_udev
```

### Custom Installation Path

```yaml
- hosts: workstations
  become: true
  vars:
    trezor_udev_rules_path: "/lib/udev/rules.d/51-trezor.rules"
  roles:
    - guiand888.workstation.trezor_udev
```

## How It Works

1. **Check Existing**: Verifies if Trezor udev rules already exist
2. **Backup**: Creates timestamped backup of existing rules (optional)
3. **Download**: Fetches official rules from Trezor's data repository
4. **Install**: Places rules in `/etc/udev/rules.d/51-trezor.rules`
5. **Reload**: Reloads udev rules with `udevadm control --reload-rules`
6. **Trigger**: Triggers udev to apply new rules with `udevadm trigger`
7. **Verify**: Confirms successful installation

## What The Rules Do

The udev rules configure:
- **Device Permissions**: Sets mode 0660 for Trezor devices
- **Group Access**: Assigns devices to `plugdev` group
- **User Access**: Enables `uaccess` and `udev-acl` for logged-in users
- **Device Links**: Creates `/dev/trezor%n` symlinks for easy access
- **HID Support**: Configures both USB and HID raw device access

## Verification

After installation, verify Trezor detection:

```bash
# Check udev rules
ls -la /etc/udev/rules.d/51-trezor.rules

# Test device detection (with Trezor connected)
lsusb | grep -i trezor
udevadm info --name=/dev/trezor0
```

## Troubleshooting

### Device Not Detected

```bash
# Reload rules manually
sudo udevadm control --reload-rules
sudo udevadm trigger

# Check if device appears
lsusb
dmesg | tail
```

### Permission Issues

```bash
# Check user groups
groups $USER

# Add user to plugdev group if needed
sudo usermod -a -G plugdev $USER
```

### Rule Conflicts

```bash
# Check for conflicting rules
ls -la /etc/udev/rules.d/*trezor*
ls -la /lib/udev/rules.d/*trezor*
```

## File Locations

- **Rules File**: `/etc/udev/rules.d/51-trezor.rules`
- **Backup Files**: `/etc/udev/rules.d/51-trezor.rules.backup.<timestamp>`
- **Official Source**: `https://data.trezor.io/udev/51-trezor.rules`

## License

MIT-0

## Author Information

Created for the `guiand888.workstation` collection.
