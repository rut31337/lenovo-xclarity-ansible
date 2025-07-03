# Lenovo XClarity Ansible Roles

A comprehensive collection of Ansible roles for managing Lenovo XClarity BMC controllers via Redfish API. This project provides modular, reusable roles for power management, user management, and common BMC operations.

## Architecture

This project has been restructured into modular Ansible roles for better maintainability and reusability:

### Roles Structure

```
roles/
├── lenovo_xclarity_common/     # Common BMC connectivity and validation
├── lenovo_xclarity_power/      # Power management and PXE boot
└── lenovo_xclarity_user/       # User account management
```

### Available Playbooks

- **`lenovo_xclarity_power_playbook.yml`** - Power management operations
- **`lenovo_xclarity_user_playbook.yml`** - User management operations  
- **`lenovo_xclarity_combined_playbook.yml`** - Combined operations playbook

## Features

### Power Management
- **Power Control**: Power on, power off (graceful/forced), reset, and status checking
- **PXE Boot Configuration**: One-time PXE boot setup with automatic system reset
- **Smart Monitoring**: Active power state monitoring until desired state is reached
- **Intelligent Validation**: Graceful exits and validation for invalid operations

### User Management
- **User Operations**: Create, update, delete, and query user accounts
- **Password Validation**: Comprehensive password complexity checking
- **Role Management**: Support for Administrator, Operator, ReadOnly, and PowerUser roles
- **Custom Roles**: PowerUser role with specific console and power privileges

### Common Features
- **BMC Connectivity**: Automatic connection validation and service discovery
- **Error Handling**: Comprehensive error handling and graceful failures
- **Flexible Configuration**: Extensive variable customization
- **Tagged Execution**: Granular control with Ansible tags

## Prerequisites

- **Ansible**: Version 2.9 or higher
- **Python**: Version 3.6 or higher
- **Network Access**: HTTP/HTTPS connectivity to BMC controllers
- **Credentials**: Valid BMC username and password with appropriate privileges

## Requirements

### System Requirements

- **Operating System**: Linux, macOS, or Windows (via WSL)
- **Python**: 3.6+ (3.8+ recommended)
- **Ansible**: 2.9+ (4.0+ recommended)
- **Network**: HTTPS access to BMC on port 443

### Minimum Versions Tested

| Component | Minimum | Recommended | Notes |
|-----------|---------|-------------|-------|
| Ansible | 2.9.0 | 4.0+ | Core functionality |
| Python | 3.6 | 3.8+ | Better performance |

## Quick Start

### 1. Clone the repository
```bash
git clone https://github.com/rut31337/lenovo-xclarity-ansible.git
cd lenovo-xclarity-ansible
```

### 2. Install Ansible
Ensure Ansible 2.9+ is installed on your system.

### 3. Basic Usage Examples

#### Power Management
```bash
# Check power status
ansible-playbook lenovo_xclarity_power_playbook.yml \
  -e bmc_hostname=192.168.1.100 \
  -e bmc_username=admin \
  -e bmc_password=password123 \
  -e power_action=status

# Power on system
ansible-playbook lenovo_xclarity_power_playbook.yml \
  -e bmc_hostname=192.168.1.100 \
  -e bmc_username=admin \
  -e bmc_password=password123 \
  -e power_action=on

# Configure PXE boot and reset
ansible-playbook lenovo_xclarity_power_playbook.yml \
  -e bmc_hostname=192.168.1.100 \
  -e bmc_username=admin \
  -e bmc_password=password123 \
  -e enable_pxe_boot_and_reset=true
```

#### User Management
```bash
# Create new user
ansible-playbook lenovo_xclarity_user_playbook.yml \
  -e bmc_hostname=192.168.1.100 \
  -e bmc_username=admin \
  -e bmc_password=password123 \
  -e target_username=newuser \
  -e target_password=NewPassword123! \
  -e user_action=create \
  -e user_role=Operator

# Update user password
ansible-playbook lenovo_xclarity_user_playbook.yml \
  -e bmc_hostname=192.168.1.100 \
  -e bmc_username=admin \
  -e bmc_password=password123 \
  -e target_username=existinguser \
  -e target_password=UpdatedPassword456! \
  -e user_action=update_password
```

#### Combined Operations
```bash
# Power and user operations together
ansible-playbook lenovo_xclarity_combined_playbook.yml \
  -e bmc_hostname=192.168.1.100 \
  -e bmc_username=admin \
  -e bmc_password=password123 \
  -e operation_type=both \
  -e power_action=on \
  -e target_username=newuser \
  -e target_password=NewPassword123! \
  -e user_action=create
```

### Real-world Example: Complete Server Setup

This example demonstrates a common use case - creating a service account and configuring the server for PXE boot:

```bash
# Create a service user account and configure PXE boot with reset
ansible-playbook lenovo_xclarity_combined_playbook.yml \
  -e bmc_hostname=169.60.175.224 \
  -e bmc_username=root \
  -e bmc_password=adminPassword123! \
  -e operation_type=both \
  -e target_username=serviceuser \
  -e target_password=ServicePass123! \
  -e user_action=create \
  -e user_role=PowerUser \
  -e enable_pxe_boot_and_reset=true
```

**What this does:**
1. **Connects** to the BMC using admin credentials
2. **Creates** a new user `serviceuser` with PowerUser role (if it doesn't exist)
3. **Sets** the password to `ServicePass123!` with full validation
4. **Configures** one-time PXE boot override
5. **Resets** the server to boot from network

**Result:** A new service account is created and the server reboots from PXE network, ready for OS deployment or maintenance.

## Role Usage

### Using Roles in Your Playbooks

```yaml
---
- name: Custom BMC Management
  hosts: localhost
  roles:
    - role: lenovo_xclarity_power
      vars:
        bmc_hostname: "192.168.1.100"
        bmc_username: "admin"
        bmc_password: "password123"
        power_action: "on"
    
    - role: lenovo_xclarity_user
      vars:
        bmc_hostname: "192.168.1.100"
        bmc_username: "admin"
        bmc_password: "password123"
        target_username: "serviceuser"
        target_password: "ServicePassword123!"
        user_action: "create"
        user_role: "Operator"
```

## Configuration

### Common Variables

| Variable | Required | Description | Default |
|----------|----------|-------------|---------|
| `bmc_hostname` | Yes | BMC IP address or hostname | - |
| `bmc_username` | Yes | BMC username | - |
| `bmc_password` | Yes | BMC password | - |
| `bmc_validate_certs` | No | Validate SSL certificates | `false` |
| `bmc_timeout` | No | Connection timeout (seconds) | `30` |

### Power Management Variables

| Variable | Required | Description | Default |
|----------|----------|-------------|---------|
| `power_action` | No | Power action to perform | `status` |
| `enable_pxe_boot_and_reset` | No | Enable PXE boot with reset | `false` |

### User Management Variables

| Variable | Required | Description | Default |
|----------|----------|-------------|---------|
| `target_username` | Yes | Target username | - |
| `target_password` | Yes* | Target password | - |
| `user_action` | No | User action to perform | `status` |
| `user_role` | No | User role to assign | `ReadOnly` |

*Required for create/update operations

## Advanced Usage

### Using Tags for Selective Execution

```bash
# Run only connectivity tests
ansible-playbook lenovo_xclarity_power_playbook.yml \
  -e bmc_hostname=192.168.1.100 \
  -e bmc_username=admin \
  -e bmc_password=password123 \
  --tags connectivity

# Run only power operations
ansible-playbook lenovo_xclarity_power_playbook.yml \
  -e bmc_hostname=192.168.1.100 \
  -e bmc_username=admin \
  -e bmc_password=password123 \
  --tags power

# Skip validation
ansible-playbook lenovo_xclarity_user_playbook.yml \
  -e bmc_hostname=192.168.1.100 \
  -e bmc_username=admin \
  -e bmc_password=password123 \
  --skip-tags validation
```

### Using Ansible Vault for Credentials

```bash
# Create encrypted credentials file
ansible-vault create vault.yml

# Content of vault.yml:
# bmc_password: your_secret_password
# target_password: new_user_password

# Use with playbook
ansible-playbook lenovo_xclarity_user_playbook.yml \
  --ask-vault-pass \
  -e @vault.yml \
  -e bmc_hostname=192.168.1.100 \
  -e bmc_username=admin \
  -e target_username=newuser \
  -e user_action=create
```

## Password Requirements

User passwords must meet these requirements:
- **Length**: 10-32 characters
- **Letters**: At least one letter (A-Z or a-z)
- **Numbers**: At least one number (0-9)
- **Complexity**: At least 2 of: uppercase, lowercase, special characters
- **Uniqueness**: Cannot match username or its reverse
- **Characters**: No more than 2 consecutive identical characters
- **Allowed Characters**: A-Z, a-z, 0-9, and ~`!@#$%^&*()-+={}[]|:;"'<>,?/._

## Available Tags

### Common Tags
- `always`: Critical tasks that always run
- `connectivity`: BMC connection tests
- `validation`: Input validation
- `info`: Information display

### Power Management Tags
- `power`: Power management operations
- `pxe`: PXE boot configuration
- `boot`: Boot settings management

### User Management Tags
- `users`: User account operations
- `create`: User creation operations
- `update_password`: Password update operations
- `delete`: User deletion operations
- `status`: User status display
- `role_management`: Role assignment operations


## Troubleshooting

### Common Issues

1. **Connection Timeout**: Increase `bmc_timeout` value
2. **SSL Certificate Errors**: Set `bmc_validate_certs: false`
3. **Permission Denied**: Ensure BMC credentials have appropriate privileges
4. **Password Validation Failures**: Check password complexity requirements

### Debug Mode

Enable verbose output for troubleshooting:
```bash
ansible-playbook lenovo_xclarity_power_playbook.yml -vvv \
  -e bmc_hostname=192.168.1.100 \
  -e bmc_username=admin \
  -e bmc_password=password123
```

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests if applicable
5. Submit a pull request

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Support

For issues and questions:
- Create an issue on GitHub
- Check the role-specific README files for detailed documentation
- Review the troubleshooting section above

## Author

Created by prutledg for Lenovo XClarity BMC management automation. 