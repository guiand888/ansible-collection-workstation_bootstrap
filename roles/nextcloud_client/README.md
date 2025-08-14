# Nextcloud Client Role

Installs Nextcloud desktop client and configures automatic pause/resume functionality on metered network connections to save bandwidth and data costs.

## Features

- **Nextcloud Installation**: Installs Nextcloud desktop client from system packages
- **Metered Network Detection**: Automatically detects metered connections (mobile, cellular, marked connections)
- **Smart Pause/Resume**: Pauses sync on metered networks, resumes on unmetered networks
- **Background Monitoring**: Runs as a systemd user service with configurable check intervals
- **Desktop Notifications**: Shows `notify-send` notifications when pausing/resuming sync
- **Comprehensive Logging**: Logs all network state changes and actions taken
- **Security Hardening**: Service runs with restricted permissions and system call filtering

## Requirements

- Linux system with systemd (tested on Fedora)
- NetworkManager (for optimal metered connection detection)
- Desktop environment with libnotify support (for notifications)

## Role Variables

### Control Variables

- `nextcloud_client_enable` (default: `true`): Whether to configure Nextcloud client
- `nextcloud_client_install` (default: `true`): Whether to install Nextcloud client if not present
- `nextcloud_client_metered_pause` (default: `true`): Whether to enable metered network monitoring

### Package Configuration

- `nextcloud_client_package_name` (default: `"nextcloud-client"`): System package name for Nextcloud

### Monitoring Configuration

- `nextcloud_client_script_dir` (default: `"/usr/local/bin"`): Directory for monitoring script
- `nextcloud_client_script_name` (default: `"nextcloud-metered-monitor"`): Script filename
- `nextcloud_client_service_name` (default: `"nextcloud-metered-monitor"`): Systemd service name
- `nextcloud_client_check_interval` (default: `30`): Network check interval in seconds

### File Locations

- `nextcloud_client_log_file` (default: `"$HOME/.local/share/nextcloud-metered-monitor.log"`): Log file path
- `nextcloud_client_config_dir` (default: `"$HOME/.config/Nextcloud"`): Nextcloud config directory

## How It Works

### Network Detection

1. **NetworkManager Integration**: Queries active connections for metered status
2. **Connection Type Analysis**: Identifies mobile/cellular connections (GSM, CDMA, Bluetooth)
3. **Interface Pattern Matching**: Detects mobile interfaces (wwan, ppp, usb patterns)
4. **Manual Override**: Respects user-configured metered connection settings

### Sync Management

1. **Process Monitoring**: Tracks Nextcloud background process status
2. **Graceful Pause**: Terminates client process when metered connection detected
3. **Automatic Resume**: Restarts client when returning to unmetered connection
4. **State Tracking**: Uses marker files to distinguish manual vs automatic stops

### Desktop Notifications

- **Pause**: "Sync paused - metered connection detected" with network-wireless icon
- **Resume**: "Sync resumed - unmetered connection" with network-wireless icon
- **Fallback**: Gracefully handles systems without `notify-send` (headless installs)
- **Integration**: Works with GNOME, KDE, XFCE, and most desktop environments

## Example Playbooks

### Basic Usage

```yaml
- hosts: workstations
  roles:
    - guiand888.workstation.nextcloud_client
```

### Install Only (No Monitoring)

```yaml
- hosts: workstations
  vars:
    nextcloud_client_metered_pause: false
  roles:
    - guiand888.workstation.nextcloud_client
```

### Custom Check Interval

```yaml
- hosts: workstations
  vars:
    nextcloud_client_check_interval: 60  # Check every minute
  roles:
    - guiand888.workstation.nextcloud_client
```

### Skip Installation (Configure Monitoring Only)

```yaml
- hosts: workstations
  vars:
    nextcloud_client_install: false
  roles:
    - guiand888.workstation.nextcloud_client
```

## Service Management

### Manual Control

```bash
# Check service status
systemctl --user status nextcloud-metered-monitor

# Stop/start monitoring
systemctl --user stop nextcloud-metered-monitor
systemctl --user start nextcloud-metered-monitor

# View logs
journalctl --user -u nextcloud-metered-monitor -f

# View monitoring log file
tail -f ~/.local/share/nextcloud-metered-monitor.log
```

### Configuration Files

- **Script**: `/usr/local/bin/nextcloud-metered-monitor`
- **Service**: `~/.config/systemd/user/nextcloud-metered-monitor.service`
- **Log**: `~/.local/share/nextcloud-metered-monitor.log`
- **State**: `~/.nextcloud_paused_by_monitor` (temporary marker file)

## Behavior Examples

| Network State | Nextcloud Status | Action Taken | Notification |
|---------------|------------------|--------------|-------------|
| Unmetered → Metered | Running | Pause sync | "Sync paused - metered connection detected" |
| Metered → Unmetered | Paused by monitor | Resume sync | "Sync resumed - unmetered connection" |
| Metered → Unmetered | Manually stopped | No action | None |
| Any → Any | Already correct state | No action | None |

## Troubleshooting

### Service Not Starting

```bash
# Check service logs
journalctl --user -u nextcloud-metered-monitor

# Verify script permissions
ls -la /usr/local/bin/nextcloud-metered-monitor

# Test script manually
/usr/local/bin/nextcloud-metered-monitor
```

### Network Detection Issues

```bash
# Test NetworkManager detection
nmcli connection show --active
nmcli -t -f connection.metered connection show <connection_name>

# Check for mobile interfaces
ip route | grep -E "(wwan|ppp|usb)"
```

### Notifications Not Appearing

```bash
# Test notify-send availability
which notify-send
notify-send "Test" "Notification test"

# Check desktop environment
echo $XDG_CURRENT_DESKTOP
ps aux | grep notification
```

### Nextcloud Not Responding

```bash
# Check Nextcloud process
ps aux | grep nextcloud

# Manually start Nextcloud
nextcloud --background

# Remove stuck marker file
rm -f ~/.nextcloud_paused_by_monitor
```

## License

MIT-0

## Author Information

Created for the `guiand888.workstation` collection.
