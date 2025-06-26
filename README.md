# Lenovo XClarity Ansible Playbook

A comprehensive Ansible playbook for managing Lenovo XClarity BMC controllers via Redfish API. This playbook provides power management, PXE boot configuration, and system monitoring capabilities for Lenovo servers.

## Features

- **Power Management**: Power on, power off (graceful/forced), reset, and status checking
- **PXE Boot Configuration**: One-time PXE boot setup with automatic system reset
- **Intelligent Monitoring**: Active power state monitoring until desired state is reached
- **Error Handling**: Graceful exits and validation for invalid operations

## Prerequisites

- **Ansible**: Version 2.9 or higher
- **Python**: Version 3.6 or higher
- **Network Access**: HTTP/HTTPS connectivity to BMC controllers
- **Credentials**: Valid BMC username and password with administrative privileges

## Installation

1. **Clone the repository**:
   ```bash
   git clone https://github.com/rut31337/lenovo-xclarity-ansible.git
   cd lenovo-xclarity-ansible
   ```

2. **Install Ansible** (if not already installed):
   ```bash
   # Ubuntu/Debian
   sudo apt update && sudo apt install ansible

   # RHEL/CentOS
   sudo yum install ansible

   # macOS
   brew install ansible

   # pip
   pip install ansible
   ```

## Configuration

### Required Variables

The playbook requires these variables to be passed as extra vars:

| Variable | Required | Description | Example |
|----------|----------|-------------|---------|
| `bmc_hostname` | Yes | BMC IP address or hostname | `192.168.1.100` |
| `bmc_username` | Yes | BMC username | `admin` |
| `bmc_password` | Yes | BMC password | `password123` |
| `power_action` | No | Power action to perform | `on`, `off`, `reset`, `status` |
| `enable_pxe_boot_and_reset` | No | Enable PXE boot with auto reset (default: false) | `true`, `false` |

## Usage Examples

### Basic Power Management

**Check power status**:
```bash
ansible-playbook lenovo_xclarity_playbook.yml \
  -e bmc_hostname=192.168.1.100 \
  -e bmc_username=admin \
  -e bmc_password=password123 \
  -e power_action=status
```

**Power on system**:
```bash
ansible-playbook lenovo_xclarity_playbook.yml \
  -e bmc_hostname=192.168.1.100 \
  -e bmc_username=admin \
  -e bmc_password=password123 \
  -e power_action=on
```

**Power off system (graceful)**:
```bash
ansible-playbook lenovo_xclarity_playbook.yml \
  -e bmc_hostname=192.168.1.100 \
  -e bmc_username=admin \
  -e bmc_password=password123 \
  -e power_action=off
```

**Force power off**:
```bash
ansible-playbook lenovo_xclarity_playbook.yml \
  -e bmc_hostname=192.168.1.100 \
  -e bmc_username=admin \
  -e bmc_password=password123 \
  -e power_action=force_off
```

**Reset system**:
```bash
ansible-playbook lenovo_xclarity_playbook.yml \
  -e bmc_hostname=192.168.1.100 \
  -e bmc_username=admin \
  -e bmc_password=password123 \
  -e power_action=reset
```

### PXE Boot Configuration

**Configure PXE boot with automatic reset**:
```bash
ansible-playbook lenovo_xclarity_playbook.yml \
  -e bmc_hostname=192.168.1.100 \
  -e bmc_username=admin \
  -e bmc_password=password123 \
  -e enable_pxe_boot_and_reset=true
```

### Advanced Examples

**Using environment variables**:
```bash
export BMC_HOST=192.168.1.100
export BMC_USER=admin
export BMC_PASS=password123

ansible-playbook lenovo_xclarity_playbook.yml \
  -e bmc_hostname=$BMC_HOST \
  -e bmc_username=$BMC_USER \
  -e bmc_password=$BMC_PASS \
  -e power_action=on
```

**Using Ansible Vault for passwords**:
```bash
# Create encrypted password file
ansible-vault create secrets.yml

# Content of secrets.yml:
# bmc_password: password123

# Run playbook with vault
ansible-playbook lenovo_xclarity_playbook.yml \
  --ask-vault-pass \
  -e @secrets.yml \
  -e bmc_hostname=192.168.1.100 \
  -e bmc_username=admin \
  -e power_action=on
```

## Playbook Behavior

### Graceful Exits

The playbook will exit gracefully in these scenarios:
- System is already in the desired power state
- Reset requested on a powered-off system
- Conflicting parameters (enable_pxe_boot_and_reset + manual power action)

### Power State Monitoring

The playbook actively monitors power state changes:
- **Retries**: Up to 30 attempts (2.5 minutes total)
- **Delay**: 5 seconds between checks
- **Smart Validation**: Verifies the expected end state based on action

### PXE Boot Logic

- **PXE + Auto Reset**: Automatically enables PXE boot even if not explicitly set
- **Power State Aware**: Powers on if system is off, resets if system is on
- **One-time Boot**: Configures single PXE boot (reverts after boot)

## Tag Usage

Filter execution using Ansible tags:

```bash
# Run only connectivity tests
ansible-playbook ... --tags connectivity

# Run only power operations
ansible-playbook ... --tags power

# Run only PXE operations
ansible-playbook ... --tags pxe

# Skip debug output
ansible-playbook ... --skip-tags debug
```

Available tags:
- `always`: Critical tasks that always run
- `connectivity`: BMC connection tests
- `power`: Power management operations
- `pxe`: PXE boot configuration
- `boot`: Boot settings management
- `debug`: Verbose debug output
- `validation`: Input validation

## Troubleshooting

### Common Issues

**Connection refused**:
- Verify BMC IP address and network connectivity
- Check firewall settings
- Ensure HTTPS (port 443) is accessible

**Authentication failed**:
- Verify username and password
- Check account is not locked
- Ensure account has administrative privileges

**SSL/TLS errors**:
- The playbook uses `validate_certs: false` for self-signed certificates
- For production, consider proper certificate management

**Power action failed**:
- Check current power state first (`power_action=status`)
- Some actions require specific power states (e.g., reset needs system to be on)
- Wait for previous power operations to complete

### Debug Mode

Enable verbose output for troubleshooting:

```bash
ansible-playbook -vvv lenovo_xclarity_playbook.yml \
  -e bmc_hostname=192.168.1.100 \
  -e bmc_username=admin \
  -e bmc_password=password123 \
  -e power_action=status
```

### Timeout Issues

If operations timeout:
- Increase retry count by modifying the playbook
- Check network latency to BMC
- Some BMCs may be slower to respond

## Security Considerations

- **Credentials**: Never commit passwords to version control
- **Network**: Use secure networks for BMC communication
- **Certificates**: Consider proper certificate validation for production
- **No Inventory Required**: The playbook runs locally without needing inventory files

## Compatible Hardware

This playbook is designed for Lenovo servers with XClarity controllers that support Redfish API, including:
- ThinkSystem SR series
- ThinkAgile series  
- System x series (with XClarity)

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test with your hardware
5. Submit a pull request

## License

This project is open source. Please refer to the license file for details.

## Support

For issues and questions:
- Open an issue on GitHub
- Check Lenovo XClarity documentation
- Verify Redfish API compatibility 