# nix

Installs and configures the distro-packaged, multi-user (daemon) Nix on Fedora and openSUSE Tumbleweed: no upstream installer, SELinux left enforcing. System layer only; per-user tooling (direnv, nix-direnv, shell hooks) belongs in dotfiles.

## Requirements

- Fedora or openSUSE Tumbleweed (`os_family` RedHat or Suse); other systems are skipped with a message.
- Root privileges.

## What it does

1. Installs the packages: Fedora `nix`, `nix-daemon`, `nix-legacy` (the last two are only weak dependencies, so they are named); openSUSE `nix` (single package).
2. Writes `/etc/nix/nix.workstation.conf` from `nix_settings` and adds one `!include` line to the packaged `/etc/nix/nix.conf`, which is otherwise left as shipped. Defaults: `sandbox = true`, `extra-experimental-features = nix-command flakes`, `extra-nix-path = nixpkgs=flake:nixpkgs`, plus `ssl-cert-file` per distro. `extra-` keys append, so they are harmless where the package already sets them (Fedora). `trusted-users` is deliberately not set (root-equivalent).
3. openSUSE only: adds `nix_users` to the `nix-users` group. The daemon socket is `0660 root:nix-users` there, so membership is mandatory. Fedora has no such group.
4. openSUSE with SELinux enabled: labels `/var/lib/nix/nix/daemon-socket` as `var_run_t` (`semanage fcontext` + `restorecon`). The policy has no nix module, so without this systemd is denied creating the socket (`avc: denied { create } ... init_t ... var_lib_t ... sock_file`). Disable with `nix_selinux_relabel_socket: false`.
5. Enables and starts the daemon: `nix-daemon.service` on Fedora, `nix-daemon.socket` on openSUSE.

Log out and in afterwards for `/etc/profile.d/nix*.sh` and group membership.

## Role Variables

See `defaults/main.yml`.

| Variable | Default |
| --- | --- |
| `nix_enable` | `true` |
| `nix_manage_config` | `true` |
| `nix_enable_service` | `true` |
| `nix_selinux_relabel_socket` | `true` |
| `nix_selinux_socket_type` | `var_run_t` |
| `nix_users` | `[]` (set it on openSUSE) |
| `nix_settings` | sandbox, flakes, nixpkgs path |
| `nix_ssl_cert_file` | `""` (per-distro default) |
| `nix_smoke_test` | `false` |

## Example Playbook

```yaml
- name: Configure Nix
  hosts: workstations
  become: true
  vars:
    nix_users: [guillaume]
  roles:
    - guiand888.workstation_bootstrap.nix
```

## Notes

- Verified against the Fedora 44 package (2.34.8) and the Tumbleweed RPM contents (2.35.3).
- **Not verified on a live Tumbleweed install:** the openSUSE unit and tmpfiles use `/var/lib/nix` while its `profile.d` scripts mix `/var/lib/nix` and `/nix/var` paths (packaging quirk, not patched); the `ssl-cert-file` default; zsh login sourcing of `profile.d`.
- Fedora's `sandbox = true` is already the Linux default; the setting is made explicit.

## License

GPL-3.0-or-later

## Author

Guillaume Andre (@guiand888)
