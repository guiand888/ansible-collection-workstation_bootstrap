# packman_codecs

openSUSE Tumbleweed counterpart of `ffmpeg_full`: adds a third-party codec repository and switches the installed ffmpeg libraries (HEVC, x264/x265, fdk-aac) to its builds. It also moves anything still installed from **Packman** to that repository and removes Packman.

> The name is historical. The default repository is **VideoLAN**, because Packman is discontinued on 2026-12-31 ([announcement thread](https://forums.opensuse.org/t/the-packman-project-to-discontinue-on-31-12-2026/196000)): after that date its packages get no updates. The role is not renamed, so existing playbooks keep working.

## Requirements

- openSUSE Tumbleweed (its repository URL is `/SuSE/Tumbleweed/`); other systems, including Leap, are skipped with a message.
- Root privileges, `community.general` (`zypper_repository`, `zypper_repository_info`).

## What it does

1. Adds the codec repository (`packman_codecs_repo_name` / `_url`, priority 90).
2. Switches the installed `libav*` / `libsw*` / `libpostproc*` libraries, the matching `ffmpeg-<N>` and the extra packages to it, then the optional libraries (a missing one only warns).
3. **Leaving Packman** (`tasks/legacy.ansible.yml`):
   - Finds every installed package whose RPM vendor matches `packman_codecs_legacy_vendor_regex` (case-insensitive).
   - **Pass 1** installs each one pinned to its installed version (`name=version`) from the codec repository, with `--allow-vendor-change --oldpackage`. Pinning matters: VideoLAN ships VLC 3.0.24 *and* a VLC 4.0 nightly under the same package names, and unpinned zypper picks `libvlccore9` 4.0 and conflicts with `vlc-noX` 3.0.24.
   - **Pass 2** installs what is still on the legacy vendor unpinned. Packages that the repository builds under a different version string (`libx264-165`: `20250608.<hash>` at Packman, `0.165` at VideoLAN) are not found by version in pass 1. `--oldpackage` is needed because Packman's date-based versions sort newer.
   - Removes `packman_codecs_legacy_repo_names` (only those that exist), after the switch and never before it.
   - Re-scans the vendors and warns about anything still installed from a legacy vendor (no repository carries it, so it will no longer be updated). With `packman_codecs_strict: true` that is a failure instead.
4. In check mode, prints what a real run would switch and remove (`command` tasks and the repository module do not run in check mode).

If a switch fails (for example an unresolvable dependency problem, zypper exit code 4), the run stops **before** the Packman repository is removed.

## Role Variables

See `defaults/main.yml`.

| Variable | Default |
| --- | --- |
| `packman_codecs_enable` | `true` |
| `packman_codecs_repo_name` / `_repo_url` | `videolan` / `https://download.videolan.org/SuSE/Tumbleweed/` |
| `packman_codecs_priority` | `90` |
| `packman_codecs_lib_regex` | ffmpeg library names |
| `packman_codecs_extra_packages` | `gstreamer-plugins-libav`, `vlc-codec-gstreamer` |
| `packman_codecs_optional_packages` | `libfdk-aac2`, `libx264-165`, `libx265-216` |
| `packman_codecs_switch_legacy_vendor` | `true` |
| `packman_codecs_legacy_vendor_regex` | `packman` |
| `packman_codecs_remove_legacy_repos` | `true` (set `false` to keep Packman until its end date) |
| `packman_codecs_legacy_repo_names` | `[packman]` |
| `packman_codecs_strict` | `false` |

## Example Playbook

```yaml
- name: Codecs from VideoLAN, leave Packman
  hosts: workstations
  become: true
  roles:
    - guiand888.workstation_bootstrap.packman_codecs
```

## Notes

- **aptX Bluetooth needs nothing from Packman.** `libspa-codec-bluez5-aptx.so` ships in the openSUSE `pipewire-spa-plugins-0_2` package and links `libfreeaptx0` from the main repository. The Packman-only `pipewire-aptx` package is not needed (an earlier attempt to install it was reverted).
- Tested against the live Packman and VideoLAN repositories in a Tumbleweed container (seeded with `libx264-165` and `vlc-noX` from Packman), not on every package that Packman carries.

## License

GPL-3.0-or-later

## Author

Guillaume Andre (@guiand888)
