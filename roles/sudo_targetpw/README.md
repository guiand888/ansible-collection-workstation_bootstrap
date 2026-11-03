# sudo_targetpw

Lets named users authenticate to `sudo` with their **own** password or fingerprint on openSUSE Tumbleweed, by installing a per-user `Defaults:<user> !targetpw` drop-in.

## Why

Tumbleweed's `/usr/etc/sudoers` sets `Defaults targetpw`, so `sudo` asks for the *target* user's password (root's), not yours. `pam_fprintd` then runs as root, which has no enrolled finger, and steps aside: `fprintd-verify` works, yet `sudo` never offers the fingerprint and prompts `[sudo] password for root:`. `!targetpw` makes `sudo` authenticate the invoking user, so the fingerprint prompt appears. See `man sudoers` (`targetpw`, `rootpw`, `runaspw`) and SUSE's [sudo guide](https://documentation.suse.com/smart/systems-management/html/sudo-configure-superuser-privileges/index.html).

## Requirements

- openSUSE Tumbleweed; other systems are skipped with a message.
- Root privileges.
- For the fingerprint prompt itself: an enrolled finger (`fprintd-enroll`) and `pam_fprintd` enabled. The fingerprint driver install does the latter; this role only warns when it is missing.

## What it does

1. Validates `sudo_targetpw_users` (login names only, they must exist) and the drop-in file name.
2. Writes `/etc/sudoers.d/<sudo_targetpw_dropin>` (`root:root`, `0440`) with one `Defaults:<user> !targetpw` line per user. `visudo -csf` validates the file before it is installed.
3. Warns when a `NOPASSWD` rule exists anywhere in `/etc/sudoers.d`: it short-circuits `sudo` before PAM, so no prompt of any kind appears. The role does not remove it.
4. Asserts `sudo -l -U <user>` reports `!targetpw` (not in check mode), and warns when `pam-config -q --fprintd` fails.

The override is deliberately per named user. Never make it global: the stock `ALL ALL=(ALL) ALL` rule is only safe together with `targetpw`.

## Role Variables

See `defaults/main.yml`.

| Variable | Default |
| --- | --- |
| `sudo_targetpw_enable` | `true` |
| `sudo_targetpw_users` | `[]` (required: the play usually runs under `become`) |
| `sudo_targetpw_dropin` | `10-fingerprint` (no `.`, no trailing `~`) |
| `sudo_targetpw_visudo` | `/usr/sbin/visudo` |
| `sudo_targetpw_check_pam_fprintd` | `true` |

## Example Playbook

```yaml
- name: Authenticate sudo as the user
  hosts: workstations
  become: true
  vars:
    sudo_targetpw_users: [guillaume]
  roles:
    - guiand888.workstation_bootstrap.sudo_targetpw
```

## Notes

- Run it **last** in a playbook: once applied, later `become: sudo` tasks expect the invoking user's credentials instead of root's. Running the play as root (for example `su -`) is unaffected.
- A later `Defaults ... targetpw` line in another `/etc/sudoers.d` file would win over this one (files are read in lexical order); the end-state assertion catches that.

## License

GPL-3.0-or-later

## Author

Guillaume Andre (@guiand888)
