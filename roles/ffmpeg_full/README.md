FFmpeg Full
===========

Swaps the restricted `ffmpeg-free` package with the full `ffmpeg` package on Fedora systems and updates multimedia-related package groups for complete codec support.

Requirements
------------

- Ansible 2.12 or later
- Fedora Linux
- RPM Fusion repositories should be enabled for full ffmpeg package
- Root/sudo privileges

**Testing Status**: Designed for Fedora systems with DNF package manager.

Role Variables
--------------

The following variables can be configured in `defaults/main.yml`:

- `ffmpeg_full_swap_packages: true` - Whether to swap ffmpeg-free with full ffmpeg
- `ffmpeg_full_update_multimedia_groups: true` - Whether to update multimedia and sound-video groups
- `ffmpeg_full_exclude_packagekit: true` - Whether to exclude PackageKit-gstreamer-plugin during group updates
- `ffmpeg_full_install_weak_deps: false` - Whether to install weak dependencies during group updates

Dependencies
------------

None. However, it's recommended to have RPM Fusion repositories configured before running this role.

Example Playbook
----------------

```yaml
- hosts: workstations
  become: true
  roles:
    - role: ffmpeg_full
```

Or with custom configuration:

```yaml
- hosts: workstations
  become: true
  roles:
    - role: ffmpeg_full
      ffmpeg_full_install_weak_deps: true
      ffmpeg_full_exclude_packagekit: false
```

License
-------

GPL-3.0-only

Author Information
------------------

Guillaume Andre (@guiand888)
