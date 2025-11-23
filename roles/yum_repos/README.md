YUM/DNF Repository Configuration
================================

Installs and manages YUM/DNF repository configuration files on Fedora/RHEL systems. Supports both default and custom repository configurations with safe backup and idempotent operations.

Requirements
------------

- Ansible 2.12 or later
- Fedora/RHEL/CentOS system with YUM/DNF package manager
- Root/sudo privileges for repository file management

**Testing Status**: Designed for Fedora systems with DNF.

Role Variables
--------------

The following variables can be configured in `defaults/main.yml`:

- `yum_repos_enable: true` - Whether to enable repository installation
- `yum_repos_install_dir: "/etc/yum.repos.d"` - Target directory for repo files
- `yum_repos_refresh_cache: true` - Whether to refresh DNF cache after installation
- `yum_repos_custom_repos_dir: ""` - Path to custom repository files (e.g., from inventory)

Dependencies
------------

None.

Example Playbook
----------------

### Basic Usage (with inventory-based repos)

```yaml
- hosts: ga_workstations
  become: true
  roles:
    - role: yum_repos
      yum_repos_custom_repos_dir: "{{ inventory_dir }}/group_vars/ga_workstations/yum.repos.d"
```

### Using Magic Variables (Recommended)

The role supports Ansible magic variables for robust, portable path resolution:

```yaml
# In inventories/gafr_endpoints/group_vars/ga_workstations/yum_repos.yml
yum_repos_enable: true

# Dynamic group reference using group_names magic variable
# Automatically resolves to the current group's directory
yum_repos_custom_repos_dir: "{{ inventory_dir }}/group_vars/{{ group_names[0] }}/yum.repos.d"

yum_repos_backup_existing: true
yum_repos_refresh_cache: true
```

### Disable Role

```yaml
- hosts: ga_workstations
  become: true
  roles:
    - role: yum_repos
      yum_repos_enable: false
```

### Custom Backup Location

```yaml
- hosts: ga_workstations
  become: true
  roles:
    - role: yum_repos
      yum_repos_backup_dir: "/var/backups/yum.repos.d"
      yum_repos_custom_repos_dir: "{{ inventory_dir }}/group_vars/ga_workstations/yum.repos.d"
```

### Per-Host Customization

In `inventories/gafr_endpoints/host_vars/{hostname}/vars.yml`:

```yaml
yum_repos_custom_repos_dir: "{{ inventory_dir }}/host_vars/{{ inventory_hostname }}/yum.repos.d"
```

What This Role Does
-------------------

1. **System Check**: Verifies YUM/DNF support (`/etc/yum.repos.d` exists)
2. **Backup**: Creates backups of existing repo files when replacing them (per-file backups created alongside originals using Ansible's backup mechanism)
3. **Install**: Copies repository files from configured source directory
4. **Cache Refresh**: Refreshes DNF metadata cache (if enabled)
5. **Reporting**: Displays installation summary and backup location

**Key Features:**
- Idempotent: Safe to run multiple times
- Graceful Handling: Skips if system doesn't support YUM/DNF
- Safe Backups: Timestamped backups prevent data loss
- Flexible: Supports both group and per-host customization
- Check Mode Compatible: Works with `--check` flag

Repository File Structure
-------------------------

Repository files should be stored in a directory with `.repo` extension files:

```
inventories/gafr_endpoints/group_vars/ga_workstations/yum.repos.d/
├── 1password.repo
├── cloudflare-warp.repo
├── fedora-cisco-openh264.repo
├── hashicorp.repo
├── mullvad.repo
├── netbird.repo
├── openziti.repo
├── rpmfusion-free-updates-testing.repo
├── rpmfusion-free-updates.repo
├── rpmfusion-free.repo
├── rpmfusion-nonfree-nvidia-driver.repo
├── rpmfusion-nonfree-steam.repo
├── rpmfusion-nonfree-updates-testing.repo
├── rpmfusion-nonfree-updates.repo
├── rpmfusion-nonfree.repo
├── tailscale.repo
├── teamviewer.repo
└── vscode.repo
```

Each `.repo` file follows standard YUM/DNF INI format:

```ini
[repository-id]
name=Repository Name
baseurl=https://example.com/repo
enabled=1
gpgcheck=1
gpgkey=https://example.com/key.asc
```

Magic Variables
---------------

The role uses Ansible magic variables for robust, portable path resolution:

- `inventory_dir` - Directory containing the inventory file (recommended for portability)
- `group_names` - List of groups the current host belongs to (use `group_names[0]` for primary group)
- `inventory_hostname` - Current host's inventory name (for per-host customization)
- `playbook_dir` - Directory containing the playbook (alternative approach)

**Recommended approach**: Use `inventory_dir` with `group_names[0]` for dynamic, portable paths that work across different directory structures.

Backup Management
-----------------

When `yum_repos_backup_existing: true`, the role creates per-file backups using Ansible's copy module (`backup: true`). Backups are stored alongside the original `.repo` files with Ansible-generated timestamped suffixes (for example: `/etc/yum.repos.d/example.repo.20250122-143022`). The role does not create a centralized backup directory by default.

If you prefer centralized, timestamped backups (one directory containing all existing `.repo` files), add a dedicated backup task before the install step. Example:

```yaml
- name: Create centralized timestamped backup dir and copy existing repos
  ansible.builtin.shell: |
    mkdir -p "/var/backups/yum.repos.d/backup-$(date +%Y%m%d-%H%M%S)"
    cp /etc/yum.repos.d/*.repo "/var/backups/yum.repos.d/backup-$(date +%Y%m%d-%H%M%S)/" 2>/dev/null || true
  become: true
  when:
    - yum_repos_backup_existing | bool
```

This keeps centralized backup behavior explicit and under your control while keeping the default simple and idempotent (per-file backups).

Idempotency
-----------

The role is fully idempotent:
- Only copies files if they differ from source
- Backup only created if repos exist
- Cache refresh only triggered if repos changed
- Safe to run multiple times without side effects

License
-------

GPL-3.0-only

Author Information
------------------

Guillaume Andre (@guiand888)
