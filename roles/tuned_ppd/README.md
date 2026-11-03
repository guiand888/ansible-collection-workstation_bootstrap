# tuned_ppd

Gets the GNOME **Power Mode** entries (Settings > Power, quick settings) working through `tuned` on openSUSE Tumbleweed.

## Why

GNOME only speaks the power-profiles-daemon D-Bus API (`net.hadess.PowerProfiles` / `org.freedesktop.UPower.PowerProfiles`); on Fedora `tuned-ppd` translates it to tuned. openSUSE installs neither by default: `gnome-control-center` has `Recommends: ppd-server`, but `power-profiles-daemon` and `tuned-ppd` both provide `ppd-service`, so the recommendation can never be satisfied. Even with `tuned-ppd` installed, the service dies at startup under SELinux (`PermissionError: ... /var/log/tuned/tuned-ppd.log`): `/var/log/tuned` is only a `%ghost` in the RPM, the daemon creates it as `var_log_t`, and the policy expects `tuned_log_t`.

## Requirements

- openSUSE Tumbleweed; other systems are skipped with a message (Fedora ships tuned-ppd with GNOME).
- Root privileges.

## What it does

The order matters.

1. Removes `power-profiles-daemon` (same `ppd-service` capability, same D-Bus names) and installs `tuned` and `tuned-ppd`.
2. Enables and starts `tuned`, which creates `/var/log/tuned`.
3. With SELinux enabled: ensures the directory exists and runs `restorecon -Rv` on it only when `restorecon -Rnv` reports a wrong label (idempotent, and visible in `--check`).
4. Optionally writes `/etc/tuned/ppd.conf` from the variables (`tuned_ppd_manage_conf`, off by default; the defaults reproduce the shipped file exactly) and restarts `tuned-ppd` on change.
5. Enables and starts `tuned-ppd`, then asserts that `busctl --system list` shows both API names owned by `tuned-ppd`.

Log out and in afterwards if GNOME still shows no Power Mode entry: it may have cached the missing service.

## Role Variables

See `defaults/main.yml`.

| Variable | Default |
| --- | --- |
| `tuned_ppd_enable` | `true` |
| `tuned_ppd_packages` | `[tuned, tuned-ppd]` |
| `tuned_ppd_remove_conflicting` | `true` |
| `tuned_ppd_conflicting_packages` | `[power-profiles-daemon]` |
| `tuned_ppd_enable_services` | `true` |
| `tuned_ppd_log_dir` | `/var/log/tuned` |
| `tuned_ppd_selinux_relabel` | `true` |
| `tuned_ppd_manage_conf` | `false` |
| `tuned_ppd_default_profile` | `balanced` |
| `tuned_ppd_profiles` | `power-saver: powersave`, `balanced: balanced`, `performance: throughput-performance` |
| `tuned_ppd_battery_profiles` | `balanced: balanced-battery` (empty = omit the section) |

## Example Playbook

```yaml
- name: Power profiles through tuned
  hosts: workstations
  become: true
  roles:
    - guiand888.workstation_bootstrap.tuned_ppd
```

## Notes

- Selecting a profile in GNOME changes the active tuned profile. After the first start tuned-ppd applies its own mapping (`balanced`), replacing a profile set by hand with `tuned-adm profile`.
- The `auto-power-profile` GNOME extension depends on this D-Bus API, so run this role before configuring it.
- The two packaging problems (the `ppd-server`/`ppd-service` mismatch and the `%ghost` directory label) are, as far as checked, not reported on bugzilla.opensuse.org. The relabel step can be dropped once the label is fixed upstream.
- The SELinux branch is verified in a container with a stub `restorecon` and a forced `selinux` fact; the real label and the systemd/D-Bus steps are verified on a Tumbleweed host only.

## License

GPL-3.0-or-later

## Author

Guillaume Andre (@guiand888)
