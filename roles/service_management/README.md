Service Management
==================

Manages systemd services with graceful error handling for missing services. Designed to stop and disable services for battery savings or security without failing when services don't exist.

Requirements
------------

- Ansible 2.12 or later
- Linux system with systemd
- Root/sudo privileges for service management

**Testing Status**: Designed for systemd-based Linux distributions.

Role Variables
--------------

The following variables can be configured in `defaults/main.yml`:

- `service_management_services` - List of services to manage with their desired state
- `service_management_ignore_missing: true` - Whether to continue if a service doesn't exist

**Service Configuration Format:**
```yaml
service_management_services:
  - name: servicename
    enabled: false  # true/false
    state: stopped  # started/stopped/restarted
    description: "Human readable description"
```

Dependencies
------------

None.

Example Playbook
----------------

```yaml
- hosts: workstations
  become: true
  roles:
    - role: service_management
```

Or with custom services:

```yaml
- hosts: workstations
  become: true
  roles:
    - role: service_management
      service_management_services:
        - name: teamviewerd
          enabled: false
          state: stopped
          description: "TeamViewer daemon (disabled for battery savings)"
        - name: bluetooth
          enabled: false
          state: stopped
          description: "Bluetooth service (disabled for security)"
```

What This Role Does
-------------------

1. **Check Services**: Verifies each service exists before attempting to manage it
2. **Graceful Handling**: Skips missing services without failing the playbook
3. **Manage Services**: Sets the desired enabled/disabled and started/stopped state
4. **Clear Feedback**: Reports which services were managed and which were skipped

**Error Handling:**
- Services that don't exist are skipped with informative messages
- Role continues execution even if some services are missing
- Only attempts to manage services that are actually present on the system

**Default Configuration:**
- Disables and stops `teamviewerd` service for battery savings
- Can be easily extended to manage additional services

License
-------

GPL-3.0-only

Author Information
------------------

Guillaume Andre (@guiand888)
