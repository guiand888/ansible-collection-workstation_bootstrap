ansible_vault_bitwarden
========================

Installs Bitwarden CLI and ansible-vault-bw script for seamless ansible-vault password management using Bitwarden as the password store.

Requirements
------------

- A Bitwarden account with ansible-vault passwords stored as Login entries
- Internet connection for downloading Bitwarden CLI and script
- `jq` package (for Bitwarden CLI status parsing)

Role Variables
--------------

```yaml
# Enable/disable role execution
ansible_vault_bitwarden_enable: true

# Bitwarden CLI configuration
ansible_vault_bitwarden_cli_version: "2025.7.0"
ansible_vault_bitwarden_cli_url: "https://github.com/bitwarden/clients/releases/download/cli-v{{ ansible_vault_bitwarden_cli_version }}/bw-oss-linux-{{ ansible_vault_bitwarden_cli_version }}.zip"
ansible_vault_bitwarden_cli_install_dir: "{{ ansible_env.HOME }}/.local/bin"

# Script configuration
ansible_vault_bitwarden_script_url: "https://raw.githubusercontent.com/guiand888/ansible-vault-bitwarden/main/ansible-vault-bw.sh"
ansible_vault_bitwarden_script_install_dir: "{{ ansible_env.HOME }}/.local/bin"

# Optional shell aliases (disabled by default)
ansible_vault_bitwarden_aliases_enable: false
ansible_vault_bitwarden_shell_config_file: "{{ ansible_env.HOME }}/.zshrc"
```

Dependencies
------------

None.

Example Playbook
----------------

Basic usage:
```yaml
- hosts: localhost
  roles:
    - ansible_vault_bitwarden
```

With shell aliases enabled:
```yaml
- hosts: localhost
  roles:
    - role: ansible_vault_bitwarden
      vars:
        ansible_vault_bitwarden_aliases_enable: true
        ansible_vault_bitwarden_shell_config_file: "{{ ansible_env.HOME }}/.bashrc"
```

Post-Installation
-----------------

1. Login to Bitwarden: `bw login`
2. Store your ansible-vault password in Bitwarden as "ansible-vault-main" (Login type)
3. Unlock vault: `bw unlock` and set BW_SESSION environment variable
4. Use: `ansible-vault-bw.sh encrypt myfile.yml` or with aliases: `ansible-vault-bw encrypt myfile.yml`

License
-------

GPL-3.0-only

Author Information
------------------

Guillaume Andre (@guiand888)
