# Lenovo XClarity BMC User Management Playbook

This playbook provides comprehensive user management capabilities for Lenovo XClarity BMC controllers using the Redfish API.

## Features

- **Create new users** with various privilege levels including custom PowerUser role
- **Update existing user passwords** with comprehensive validation
- **Delete existing users** and free up user account slots
- **Check user status** and list existing users with detailed information
- **Automatic user existence detection** to prevent conflicts
- **Role-based access control** with standard and custom privilege levels
- **PowerUser custom role** with remote console and power management access
- **Comprehensive password validation** with complexity requirements
- **Redfish service information** display including version and vendor details
- **Enhanced error handling** and operation verification

## Prerequisites

- Ansible 2.9 or higher
- Network connectivity to the BMC
- Valid BMC administrator credentials
- Python `requests` library (for URI module)

## Compatibility

**Developed and Tested Environment:**
- **XClarity Controller API**: Version 1.15.0
- **Platform**: Whitley processors
- **Redfish Version**: Compatible with Redfish 1.6.0+

⚠️ **Important**: This playbook was specifically developed and tested on XClarity Controller API version 1.15.0 with Whitley processors. It may not function correctly on other BMC firmware versions, processor platforms, or older XClarity implementations. 

**Before using on production systems:**
- Test on a development system first
- Verify XClarity Controller API version compatibility
- Check Redfish version support (`user_action=status` will display version info)

## Usage

### Basic Syntax

```bash
ansible-playbook lenovo_xclarity_user_management.yml \
  -e "bmc_hostname=<BMC_IP_OR_HOSTNAME>" \
  -e "bmc_username=<ADMIN_USERNAME>" \
  -e "bmc_password=<ADMIN_PASSWORD>" \
  -e "target_username=<TARGET_USERNAME>" \
  -e "target_password=<TARGET_PASSWORD>" \
  -e "user_action=<ACTION>"
```

### Parameters

#### Required Parameters

| Parameter | Description | Example |
|-----------|-------------|---------|
| `bmc_hostname` | BMC IP address or hostname | `192.168.1.100` |
| `bmc_username` | BMC admin username for authentication | `admin` |
| `bmc_password` | BMC admin password for authentication | `password123` |
| `target_username` | Username to create, modify, or delete | `newuser` |

#### Conditional Parameters

| Parameter | Description | Required For | Example |
|-----------|-------------|--------------|---------|
| `target_password` | Password for the target user | `create`, `update_password` | `newpassword123` |

#### Optional Parameters

| Parameter | Description | Default | Valid Values |
|-----------|-------------|---------|--------------|
| `user_action` | Action to perform | `status` | `create`, `update_password`, `delete`, `status` |
| `user_role` | Role/privilege level for new users | `ReadOnly` | `Administrator`, `Operator`, `ReadOnly`, `PowerUser` |
| `enable_user` | Enable the user account | `true` | `true`, `false` |

### User Roles and Privileges

#### Administrator Role
- **Full BMC administrative access**
- Can manage system settings, power, boot options
- Can view all system information
- Can modify system configuration
- Standard Redfish Administrator privileges

#### Operator Role
- **Limited administrative access**
- Can manage power and boot settings
- Can view system information
- Cannot modify advanced system configuration
- Standard Redfish Operator privileges

#### ReadOnly Role
- **View-only access**
- Can read system status and information
- Cannot make any changes to the system
- Standard Redfish ReadOnly privileges

#### PowerUser Role (Custom)
- **Enhanced privileges with remote access**
- All ReadOnly capabilities
- **Remote Console Access** - Virtual media and remote console
- **Power Management** - Server power restart and control
- Custom role created using BMC CustomRole functionality

### Password Requirements

The playbook enforces comprehensive password validation:

#### Length Requirements
- **Minimum**: 10 characters
- **Maximum**: 32 characters

#### Character Requirements
- **At least one letter** (A-Z or a-z)
- **At least one number** (0-9)
- **At least 2 character types** from: uppercase, lowercase, special characters

#### Allowed Special Characters
```
~`!@#$%^&*()-+={}[]|:;"'<>,?/._
```

#### Restrictions
- **No more than 2 consecutive identical characters**
- **Cannot be the same as username** (case-insensitive)
- **Cannot be the reverse of username** (case-insensitive)

## Examples

### 1. Create a New Administrative User

```bash
ansible-playbook lenovo_xclarity_user_management.yml \
  -e "bmc_hostname=192.168.1.100" \
  -e "bmc_username=admin" \
  -e "bmc_password=admin123" \
  -e "target_username=serviceuser" \
  -e "target_password=ServiceUser123!" \
  -e "user_action=create" \
  -e "user_role=Administrator"
```

### 2. Create a PowerUser with Remote Access

```bash
ansible-playbook lenovo_xclarity_user_management.yml \
  -e "bmc_hostname=192.168.1.100" \
  -e "bmc_username=admin" \
  -e "bmc_password=admin123" \
  -e "target_username=poweruser1" \
  -e "target_password=PowerUser123!" \
  -e "user_action=create" \
  -e "user_role=PowerUser"
```

### 3. Update an Existing User's Password

```bash
ansible-playbook lenovo_xclarity_user_management.yml \
  -e "bmc_hostname=192.168.1.100" \
  -e "bmc_username=admin" \
  -e "bmc_password=admin123" \
  -e "target_username=serviceuser" \
  -e "target_password=NewPassword456!" \
  -e "user_action=update_password"
```

### 4. Delete an Existing User

```bash
ansible-playbook lenovo_xclarity_user_management.yml \
  -e "bmc_hostname=192.168.1.100" \
  -e "bmc_username=admin" \
  -e "bmc_password=admin123" \
  -e "target_username=olduser" \
  -e "user_action=delete"
```

### 5. Check User Status and List All Users

```bash
ansible-playbook lenovo_xclarity_user_management.yml \
  -e "bmc_hostname=192.168.1.100" \
  -e "bmc_username=admin" \
  -e "bmc_password=admin123" \
  -e "target_username=serviceuser" \
  -e "user_action=status"
```

### 6. Create an Operator User

```bash
ansible-playbook lenovo_xclarity_user_management.yml \
  -e "bmc_hostname=192.168.1.100" \
  -e "bmc_username=admin" \
  -e "bmc_password=admin123" \
  -e "target_username=operator1" \
  -e "target_password=Operator123!" \
  -e "user_action=create" \
  -e "user_role=Operator"
```

### 7. Create a Read-Only User

```bash
ansible-playbook lenovo_xclarity_user_management.yml \
  -e "bmc_hostname=192.168.1.100" \
  -e "bmc_username=admin" \
  -e "bmc_password=admin123" \
  -e "target_username=readonly1" \
  -e "target_password=ReadOnly123!" \
  -e "user_action=create" \
  -e "user_role=ReadOnly"
```

## Tags

You can use Ansible tags to run specific parts of the playbook:

```bash
# Run only connectivity and validation checks
ansible-playbook lenovo_xclarity_user_management.yml \
  -e "..." \
  --tags "connectivity,validation"

# Run only user creation tasks
ansible-playbook lenovo_xclarity_user_management.yml \
  -e "..." \
  --tags "create"

# Run only password update tasks
ansible-playbook lenovo_xclarity_user_management.yml \
  -e "..." \
  --tags "update_password"

# Run only user deletion tasks
ansible-playbook lenovo_xclarity_user_management.yml \
  -e "..." \
  --tags "delete"

# Check status only
ansible-playbook lenovo_xclarity_user_management.yml \
  -e "..." \
  --tags "status"
```

### Available Tags

- `always` - Always runs (connectivity, validation, basic info)
- `connectivity` - BMC connection tests and Redfish service info
- `validation` - Parameter and password validation
- `version` - Redfish version and service information
- `account_service` - Account service information
- `users` - User-related operations
- `create` - User creation
- `update_password` - Password updates
- `delete` - User deletion
- `status` - Status checks
- `verification` - Result verification
- `role_management` - Role assignment operations
- `poweruser` - PowerUser custom role operations
- `standard` - Standard role operations
- `summary` - Operation summary

## Information Displayed

The playbook provides comprehensive information about:

### Redfish Service
- Service name and version
- Redfish specification version
- BMC vendor and product information
- Service UUID

### Account Service
- Password length requirements
- Available user account endpoints
- Account service capabilities

### User Information
- Existing user accounts and their roles
- User account status (enabled/disabled)
- Available user account slots

### Operation Results
- Detailed success/failure messages
- User creation/update/deletion confirmations
- Role assignment results
- Operation summaries

## Error Handling

The playbook includes comprehensive error handling:

1. **Parameter Validation**: Validates all input parameters before execution
2. **Password Complexity Validation**: Enforces strong password requirements
3. **Connectivity Check**: Verifies BMC connectivity before proceeding
4. **User Existence Check**: Prevents duplicate user creation and validates user existence for updates/deletes
5. **Slot Availability**: Checks for available user slots before creation
6. **Role Validation**: Ensures valid role assignments
7. **Operation Verification**: Confirms successful operations with detailed feedback

## Security Considerations

1. **Password Security**: Enforces strong password policies with complexity requirements
2. **Credential Management**: Store BMC credentials securely (use Ansible Vault)
3. **Role Assignment**: Assign the minimum required privileges for the user's function
4. **PowerUser Role**: Use PowerUser role when remote console access is needed but full admin is not
5. **SSL/TLS**: BMC connections use HTTPS (certificate validation disabled by default)
6. **User Cleanup**: Delete unused accounts to minimize attack surface

## Troubleshooting

### Common Issues

1. **Connection Timeout**: Verify BMC IP address and network connectivity
2. **Authentication Failed**: Check BMC username and password
3. **No Available User Slots**: BMC has a limited number of user accounts (typically 10-16)
4. **Password Policy Violations**: Ensure passwords meet all complexity requirements
5. **Insufficient Privileges**: Ensure the authenticating user has admin rights
6. **User Already Exists**: Use `update_password` action instead of `create`
7. **User Not Found**: Verify username spelling for update/delete operations
8. **Role Assignment Failed**: Check if BMC supports the specified role

### Password Validation Errors

Common password validation failures and solutions:

- **Too short/long**: Use 10-32 characters
- **Missing letter**: Include at least one A-Z or a-z
- **Missing number**: Include at least one 0-9
- **Insufficient complexity**: Use at least 2 types: uppercase, lowercase, special characters
- **Consecutive characters**: Avoid more than 2 identical characters in a row (e.g., "aaa")
- **Username similarity**: Password cannot be the same as or reverse of username

### Debug Mode

Run with verbose output for troubleshooting:

```bash
ansible-playbook lenovo_xclarity_user_management.yml \
  -e "..." \
  -vvv
```

## Limitations

1. **Limited User Slots**: BMC controllers have a fixed number of user accounts (typically 10-16)
2. **PowerUser CustomRole**: Uses BMC's CustomRole functionality which may vary by firmware version
3. **Password Policies**: Must meet both this playbook's validation and BMC's internal requirements
4. **Session Limits**: Some BMCs limit concurrent administrative sessions

## Integration with Existing Playbook

This user management playbook can be used alongside the existing `lenovo_xclarity_playbook.yml` for comprehensive BMC management:

```bash
# First, create a user account
ansible-playbook lenovo_xclarity_user_management.yml \
  -e "bmc_hostname=192.168.1.100" \
  -e "bmc_username=admin" \
  -e "bmc_password=admin123" \
  -e "target_username=serviceuser" \
  -e "target_password=ServiceUser123!" \
  -e "user_action=create" \
  -e "user_role=PowerUser"

# Then, use the new account for system operations
ansible-playbook lenovo_xclarity_playbook.yml \
  -e "bmc_hostname=192.168.1.100" \
  -e "bmc_username=serviceuser" \
  -e "bmc_password=ServiceUser123!" \
  -e "power_action=status"
```

## Best Practices

1. **Use PowerUser Role**: For service accounts that need remote console access but not full admin
2. **Regular Password Updates**: Use `update_password` action to maintain security
3. **User Cleanup**: Delete unused accounts with `delete` action
4. **Status Monitoring**: Use `status` action to audit existing accounts
5. **Strong Passwords**: Always use complex passwords meeting all requirements
6. **Credential Rotation**: Regularly update service account passwords
7. **Principle of Least Privilege**: Assign the minimum role needed for the user's function 