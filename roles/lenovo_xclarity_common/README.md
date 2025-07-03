# Lenovo XClarity Common Role

Common tasks and variables for Lenovo XClarity BMC management via Redfish API.

## Description

This role provides shared functionality for all Lenovo XClarity BMC operations, including:
- BMC connectivity validation
- Common connection parameters
- Redfish service information display
- Parameter validation

## Requirements

- Ansible 2.9 or higher
- Network access to BMC controllers
- Valid BMC credentials with appropriate privileges

## Role Variables

### Required Variables

- `bmc_hostname`: BMC IP address or hostname
- `bmc_username`: BMC username
- `bmc_password`: BMC password

### Optional Variables

- `bmc_validate_certs`: Validate SSL certificates (default: false)
- `bmc_force_basic_auth`: Force basic authentication (default: true)
- `bmc_timeout`: Connection timeout in seconds (default: 30)

## Dependencies

None.

## Example Playbook

```yaml
---
- hosts: localhost
  roles:
    - role: lenovo_xclarity_common
      vars:
        bmc_hostname: "192.168.1.100"
        bmc_username: "admin"
        bmc_password: "password123"
```

## Tags

- `always`: Critical tasks that always run
- `connectivity`: BMC connection tests
- `validation`: Input validation
- `info`: Information display

## License

MIT

## Author Information

Created by prutledg for Lenovo XClarity BMC management. 