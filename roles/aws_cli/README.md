# AWS CLI Role

This role installs or updates AWS CLI v2 on Linux systems following the official AWS installation instructions.

## Features

- **Two distinct scenarios**: Fresh installation when not present, or update existing installation
- **Smart path detection**: Automatically detects existing installation paths for seamless updates
- **Package cleanup**: Removes old package-managed AWS CLI versions (yum/dnf) per AWS recommendations
- **Official installer**: Downloads and uses the latest AWS CLI v2 from the official AWS source
- **Idempotent operation**: Safe to run multiple times without side effects
- **Comprehensive verification**: Verifies installation success and displays version information
- **Clean operation**: Automatically cleans up temporary files after installation

## Supported Scenarios

### 1. Fresh Installation
- **When**: AWS CLI is not installed on the system
- **Action**: Downloads and performs a fresh installation to default or configured paths
- **Result**: AWS CLI v2 installed and ready to use

### 2. Update Existing Installation
- **When**: AWS CLI v2 is already installed and `aws_cli_update_existing` is `true` (default)
- **Action**: Detects current installation paths and updates in-place using `--update` flag
- **Result**: Existing installation updated to latest version, preserving configuration

### 3. Install-Only Mode
- **When**: `aws_cli_update_existing` is set to `false`
- **Action**: Only installs if not present, skips updates for existing installations
- **Result**: Ensures AWS CLI is present but doesn't modify existing installations

## Requirements

- Linux system (tested on Fedora)
- Internet connection for downloading AWS CLI installer
- sudo/root privileges for installation

## Role Variables

### Control Variables

- `aws_cli_enable` (default: `true`): Whether to install/update AWS CLI v2
- `aws_cli_update_existing` (default: `true`): Whether to update existing installations
- `aws_cli_remove_old_version` (default: `true`): Remove old yum/dnf managed versions
- `aws_cli_force_download` (default: `false`): Force re-download even if cached

### Path Configuration

- `aws_cli_bin_dir` (default: `/usr/local/bin`): Binary directory (fallback if not detected)
- `aws_cli_install_dir` (default: `/usr/local/aws-cli`): Install directory (fallback if not detected)
- `aws_cli_temp_dir` (default: `/tmp/awscli-install`): Temporary directory for downloads

### Download Configuration

- `aws_cli_download_url`: URL for AWS CLI download (automatically configured)
- `aws_cli_download_filename`: Download filename (automatically configured)

## Example Playbooks

### Basic Usage (Install or Update)

```yaml
- hosts: workstations
  roles:
    - guiand888.workstation.aws_cli
```

### Install-Only Mode (No Updates)

```yaml
- hosts: workstations
  vars:
    aws_cli_update_existing: false
  roles:
    - guiand888.workstation.aws_cli
```

### Force Fresh Download

```yaml
- hosts: workstations
  vars:
    aws_cli_force_download: true
  roles:
    - guiand888.workstation.aws_cli
```

### Custom Installation Paths

```yaml
- hosts: workstations
  vars:
    aws_cli_bin_dir: "/opt/aws/bin"
    aws_cli_install_dir: "/opt/aws/cli"
  roles:
    - guiand888.workstation.aws_cli
```

## How It Works

1. **Cleanup**: Removes old package-managed AWS CLI if present
2. **Detection**: Uses `which aws` to detect existing AWS CLI installation
3. **Path Analysis**: Extracts binary and installation directories from symlinks
4. **Decision Logic**: Determines whether fresh installation or update is needed
5. **Download**: Downloads AWS CLI v2 installer (with optional caching)
6. **Installation/Update**: Performs appropriate action based on current state
7. **Verification**: Confirms successful installation and displays version
8. **Cleanup**: Removes temporary files and directories

## Behavior Examples

| Current State | `aws_cli_update_existing` | Action Taken |
|---------------|---------------------------|--------------|
| Not installed | `true` or `false` | Fresh installation |
| Installed | `true` | Update to latest version |
| Installed | `false` | No action (skip) |

## Path Detection Logic

- **Detection**: Uses `which aws` to find current binary location
- **Binary Path**: Extracts directory from detected binary location
- **Install Path**: Follows symlink to determine installation directory
- **Fallback**: Uses configured defaults if detection fails
- **Update**: Uses detected paths for in-place updates

## Output Messages

- **Fresh Installation**: "AWS CLI v2 installed successfully: [version]"
- **Update**: "AWS CLI v2 installed successfully: [version]" (same as fresh)
- **Up-to-date**: "AWS CLI v2 already up-to-date: [version]"
- **Skip**: "AWS CLI v2 already installed (update disabled): [version]"

## License

MIT-0

## Author Information

Created for the `guiand888.workstation` collection.
