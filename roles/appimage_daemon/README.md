AppImage Daemon
===============

Installs and configures the go-appimage daemon (appimaged) for seamless AppImage integration on Linux systems. This daemon automatically creates desktop entries and handles AppImage registration.

Requirements
------------

- Ansible 2.12 or later

**Testing Status**: Designed for Linux systems with desktop environments.

Role Variables
--------------

The following variables can be configured in `defaults/main.yml`:

- `appimage_daemon_clean_old_installation: true` - Whether to clean up old AppImage installations
- `appimage_daemon_applications_directory: "~/Applications"` - Directory to store AppImage binaries
- `appimage_daemon_github_repo: "probonopd/go-appimage"` - GitHub repository for go-appimage
- `appimage_daemon_release_type: "continuous"` - Release type to download ('continuous' or 'latest')
- `appimage_daemon_binary_pattern: "appimaged-.*-x86_64.AppImage"` - Pattern to match the binary name
- `appimage_daemon_service_enabled: true` - Whether to enable and start the appimaged service
- `appimage_daemon_run_as_user: true` - Whether to run the service as user (vs system)

Dependencies
------------

None.

Example Playbook
----------------

```yaml
- hosts: workstations
  roles:
    - role: appimage_daemon
```

Or with custom configuration:

```yaml
- hosts: workstations
  roles:
    - role: appimage_daemon
      appimage_daemon_applications_directory: "/opt/appimages"
      appimage_daemon_release_type: "latest"
```

What This Role Does
-------------------

1. **Cleanup**: Removes conflicting appimagelauncher and old appimage files
2. **Download**: Fetches the latest go-appimage daemon from GitHub
3. **Install**: Places the daemon in ~/Applications directory
4. **Configure**: Sets up proper permissions and runs first-time setup
5. **Service**: Enables and starts the appimaged systemd user service

License
-------

GPL-3.0-only

Author Information
------------------

Guillaume Andre (@guiand888)
