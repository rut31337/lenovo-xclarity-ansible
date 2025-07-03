# Lenovo XClarity Power Management Role

Power management and PXE boot configuration for Lenovo XClarity BMC via Redfish API.

## Description

This role provides power management capabilities for Lenovo servers, including:
- Power on/off operations (graceful and forced)
- System reset functionality
- Power state monitoring
- PXE boot configuration with automatic reset
- Intelligent power state validation

## Requirements

- Ansible 2.9 or higher
- Network access to BMC controllers
- Valid BMC credentials with power management privileges

## Role Variables

### Required Variables

- `bmc_hostname`: BMC IP address or hostname
- `bmc_username`: BMC username
- `bmc_password`: BMC password

### Optional Variables

- `power_action`: Power action to perform (default: "status")
  - `on`: Power on the system
  - `off`: Graceful power off
  - `force_off`: Force power off
  - `reset`: Reset the system
  - `status`: Display power status only
- `enable_pxe_boot_and_reset`: Enable PXE boot with auto reset (default: false)
- `power_monitoring_retries`: Number of retries for power state monitoring (default: 30)
- `power_monitoring_delay`: Delay between monitoring attempts in seconds (default: 5)

## Dependencies

- `lenovo_xclarity_common`

## Example Playbook

```yaml
---
- hosts: localhost
  roles:
    - role: lenovo_xclarity_power
      vars:
        bmc_hostname: "192.168.1.100"
        bmc_username: "admin"
        bmc_password: "password123"
        power_action: "on"
```

### PXE Boot Example

```yaml
---
- hosts: localhost
  roles:
    - role: lenovo_xclarity_power
      vars:
        bmc_hostname: "192.168.1.100"
        bmc_username: "admin"
        bmc_password: "password123"
        enable_pxe_boot_and_reset: true
```

## Tags

- `always`: Critical tasks that always run
- `power`: Power management operations
- `pxe`: PXE boot configuration
- `boot`: Boot settings management
- `validation`: Input validation

## License

MIT

## Author Information

Created by prutledg for Lenovo XClarity BMC management. 