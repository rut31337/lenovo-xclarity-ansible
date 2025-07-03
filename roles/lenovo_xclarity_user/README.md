# Lenovo XClarity User Management Role

User account management for Lenovo XClarity BMC via Redfish API.

## Description

This role provides comprehensive user management capabilities for Lenovo BMC, including:
- User creation with password validation
- Password updates with complexity checking
- User deletion
- Role assignment (Administrator, Operator, ReadOnly, PowerUser)
- Custom PowerUser role with specific privileges
- User account discovery and validation

## Requirements

- Ansible 2.9 or higher
- Network access to BMC controllers
- Valid BMC credentials with user management privileges

## Role Variables

### Required Variables

- `bmc_hostname`: BMC IP address or hostname
- `bmc_username`: BMC username
- `bmc_password`: BMC password
- `target_username`: Username for the target user account
- `target_password`: Password for the target user account (for create/update operations)

### Optional Variables

- `user_action`: Action to perform (default: "status")
  - `create`: Create new user account
  - `update_password`: Update existing user password
  - `delete`: Delete user account
  - `status`: Display user status only
- `user_role`: Role to assign to user (default: "ReadOnly")
  - `Administrator`: Full administrative access
  - `Operator`: Operational access
  - `ReadOnly`: Read-only access
  - `PowerUser`: Custom role with console and power access
- `enable_user`: Enable the user account (default: true)

### Password Requirements

- Length: 10-32 characters
- Must contain at least one letter and one number
- Must contain at least 2 of: uppercase, lowercase, special characters
- Cannot be the same as username or its reverse
- No more than 2 consecutive identical characters

## Dependencies

- `lenovo_xclarity_common`

## Example Playbook

### Create User

```yaml
---
- hosts: localhost
  roles:
    - role: lenovo_xclarity_user
      vars:
        bmc_hostname: "192.168.1.100"
        bmc_username: "admin"
        bmc_password: "password123"
        target_username: "newuser"
        target_password: "NewPassword123!"
        user_action: "create"
        user_role: "Operator"
```

### Update Password

```yaml
---
- hosts: localhost
  roles:
    - role: lenovo_xclarity_user
      vars:
        bmc_hostname: "192.168.1.100"
        bmc_username: "admin"
        bmc_password: "password123"
        target_username: "existinguser"
        target_password: "UpdatedPassword456!"
        user_action: "update_password"
```

### Delete User

```yaml
---
- hosts: localhost
  roles:
    - role: lenovo_xclarity_user
      vars:
        bmc_hostname: "192.168.1.100"
        bmc_username: "admin"
        bmc_password: "password123"
        target_username: "olduser"
        user_action: "delete"
```

## Tags

- `always`: Critical tasks that always run
- `validation`: Input and password validation
- `users`: User account operations
- `create`: User creation operations
- `update_password`: Password update operations
- `delete`: User deletion operations
- `status`: User status display
- `role_management`: Role assignment operations

## License

MIT

## Author Information

Created by prutledg for Lenovo XClarity BMC management. 