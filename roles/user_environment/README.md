# user_environment

Per-user basics that are not packages: the **login shell** and a **home directory layout**, for named users, on Fedora and openSUSE.

## Requirements

- Root privileges.
- The login shell's package must already be installed (for example `zsh` from the `packages` role) and listed in `/etc/shells`, which the package normally takes care of.

## What it does

1. Validates `user_environment_users`: login names only, directories relative to the home directory with no `..` or empty component, every user must exist.
2. For users with a `shell`: checks it is installed, executable and listed in `/etc/shells`, then sets it (`ansible.builtin.user`). It takes effect at the next login.
3. Creates the `directories`, owned by the user. **Every parent of a nested path is created and chowned too.** `ansible.builtin.file` only sets ownership on the leaf and creates missing parents as the invoking user, which is root under `become`: `Nextcloud/Work Files` would otherwise leave `~/Nextcloud` owned by root. An existing root-owned parent is corrected on the next run.
4. Asserts the end state (shell and directory ownership; skipped in check mode), because plays that use `ignore_errors` would otherwise hide a failure.

Home directories and groups come from `getent passwd`, never from `~` or `ansible_user_id`: under `become` those resolve to root.

## Role Variables

See `defaults/main.yml`.

| Variable | Default |
| --- | --- |
| `user_environment_enable` | `true` |
| `user_environment_users` | `[]` (a list of `{name, shell?, directories?}`) |
| `user_environment_directory_mode` | `"0755"` |

## Example Playbook

```yaml
- name: Configure the user environment
  hosts: workstations
  become: true
  vars:
    user_environment_users:
      - name: guillaume
        shell: /usr/bin/zsh
        directories: [Applications, Notes, "Nextcloud/Work Files"]
  roles:
    - guiand888.workstation_bootstrap.user_environment
```

## Notes

- Run it **after** the packages that provide the shells.
- Paths are created below the home directory only; there is no way to point a directory outside it.
- Directories are never removed or emptied.

## License

GPL-3.0-or-later

## Author

Guillaume Andre (@guiand888)
