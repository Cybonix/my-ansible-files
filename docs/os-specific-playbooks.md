# OS-Specific Playbooks Documentation

This document provides information about the OS-specific configuration playbooks that address the TODO item for creating OS-specific configurations.

## Overview

Three OS-specific playbooks have been created to handle unique configuration requirements for different Linux distributions:

- **Rocky Linux Configuration**: `playbooks/os-specific/rocky-linux-config.yml`
- **Ubuntu Configuration**: `playbooks/os-specific/ubuntu-config.yml`
- **AlmaLinux Configuration**: `playbooks/os-specific/almalinux-config.yml`

## Rocky Linux Configuration

### Purpose
Configures Rocky Linux systems with enterprise-grade security, monitoring, and management features typical for RHEL-based environments.

### Target Hosts
- Group: `rocky_linux`
- Systems running Rocky Linux 8.x and 9.x

### Key Features

#### System Updates
- DNF package manager configuration
- EPEL repository installation
- PowerTools repository enablement
- Optional automatic security updates

#### Security Hardening
- SELinux configuration and enforcement
- SSH hardening with key-based authentication
- Fail2ban installation and configuration
- Firewalld setup and management

#### System Services
- Chronyd time synchronization
- Audit daemon configuration
- Log rotation setup
- Kernel parameter tuning

#### Essential Packages
```yaml
rocky_essential_packages:
  - curl, wget, vim, nano, git
  - htop, tree, unzip, tar, rsync
  - screen, tmux, net-tools
  - bind-utils, telnet, tcpdump
  - lsof, strace
  - redhat-lsb-core
  - policycoreutils-python-utils
  - setroubleshoot-server
```

### Usage
```bash
# Run against all Rocky Linux hosts
ansible-playbook playbooks/os-specific/rocky-linux-config.yml

# Run against specific host
ansible-playbook playbooks/os-specific/rocky-linux-config.yml -l rocky1.lab

# Enable automatic updates
ansible-playbook playbooks/os-specific/rocky-linux-config.yml -e "auto_update_enabled=true"
```

### Variables
- `auto_update_enabled`: Enable automatic security updates (default: false)
- `disable_root_login`: Disable SSH root login (default: true)
- `enable_fail2ban`: Install and configure fail2ban (default: true)
- `selinux_state`: SELinux enforcement mode (default: enforcing)
- `local_admin_user`: Create additional admin user (optional)

## Ubuntu Configuration

### Purpose
Configures Ubuntu systems with modern security practices, unattended upgrades, and system optimization suitable for both server and desktop environments.

### Target Hosts
- Group: `ubuntu_servers`
- Systems running Ubuntu 20.04 LTS and 22.04 LTS

### Key Features

#### Package Management
- APT cache management
- Unattended upgrades configuration
- Universe repository enablement
- Snap package support

#### Security Features
- UFW firewall configuration
- SSH hardening
- Fail2ban setup
- AppArmor enforcement

#### System Services
- systemd-timesyncd configuration
- systemd-journald optimization
- Log rotation management

#### Essential Packages
```yaml
ubuntu_essential_packages:
  - curl, wget, vim, nano, git
  - htop, tree, unzip, tar, rsync
  - screen, tmux, net-tools
  - dnsutils, telnet, tcpdump
  - lsof, strace
  - software-properties-common
  - apt-transport-https
  - ca-certificates, gnupg, lsb-release
  - build-essential
```

### Usage
```bash
# Run against all Ubuntu hosts
ansible-playbook playbooks/os-specific/ubuntu-config.yml

# Run with automatic updates enabled
ansible-playbook playbooks/os-specific/ubuntu-config.yml -e "auto_update_enabled=true"

# Configure specific firewall reset
ansible-playbook playbooks/os-specific/ubuntu-config.yml -e "firewall_reset=true"
```

### Variables
- `auto_update_enabled`: Enable system updates (default: false)
- `unattended_upgrades_enabled`: Configure automatic updates (default: true)
- `unattended_upgrades_automatic_reboot`: Allow automatic reboots (default: false)
- `unattended_upgrades_time`: Reboot time (default: "06:00")
- `apparmor_enabled`: Enable AppArmor (default: true)
- `firewall_reset`: Reset UFW to defaults (default: false)

## AlmaLinux Configuration

### Purpose
Configures AlmaLinux systems as a CentOS replacement with enterprise features, security hardening, and repository management.

### Target Hosts
- Group: `alma_linux`
- Systems running AlmaLinux 8.x and 9.x

### Key Features

#### Repository Management
- AlmaLinux official repositories
- EPEL repository integration
- PowerTools repository access
- Custom repository configuration

#### Security and Compliance
- SELinux enforcement
- Audit daemon configuration
- Security update automation
- SSH hardening

#### System Management
- Chronyd time sync
- Enhanced logging with rsyslog
- Firewalld configuration
- Kernel parameter optimization

### Usage
```bash
# Run against all AlmaLinux hosts
ansible-playbook playbooks/os-specific/almalinux-config.yml

# Enable automatic updates
ansible-playbook playbooks/os-specific/almalinux-config.yml -e "automatic_updates=true"

# Security updates only
ansible-playbook playbooks/os-specific/almalinux-config.yml -e "security_updates_only=true"
```

### Variables
- `automatic_updates`: Enable automatic updates (default: false)
- `security_updates_only`: Only security updates (default: true)
- `almalinux_repos`: Repository configuration
- `selinux_state`: SELinux mode (default: enforcing)

## Common Configuration Elements

### SSH Hardening (All OS)
All playbooks implement consistent SSH security:
- Disable root login
- Disable password authentication
- Enable public key authentication
- Configure connection timeouts
- Set maximum authentication attempts

### Firewall Configuration
- **Rocky/AlmaLinux**: firewalld
- **Ubuntu**: UFW (Uncomplicated Firewall)

### Time Synchronization
- **Rocky/AlmaLinux**: chronyd
- **Ubuntu**: systemd-timesyncd

### Log Management
All systems get custom log rotation policies and centralized logging configuration.

## Best Practices

### Running OS-Specific Playbooks

1. **Target Correctly**: Always use appropriate inventory groups
2. **Test First**: Use `--check` mode before actual execution
3. **Monitor Changes**: Review output for unexpected modifications
4. **Backup Configs**: Important configurations are automatically backed up

### Variable Management

1. **Use Group Variables**: Leverage `group_vars/` for OS-specific settings
2. **Host Overrides**: Use `host_vars/` for system-specific configurations
3. **Vault Sensitive Data**: Store passwords and keys in Ansible Vault

### Customization

Each playbook can be customized by:
- Modifying default variables in playbook
- Using group_vars overrides
- Passing extra variables at runtime
- Creating host-specific variable files

## Security Considerations

### Default Security Posture
- All playbooks enforce strict security by default
- Root login disabled via SSH
- Firewalls enabled and configured
- Automatic security updates available
- Fail2ban protection against brute force

### Compliance Features
- SELinux/AppArmor enforcement
- Audit logging enabled
- System hardening applied
- Regular security updates

## Troubleshooting

### Common Issues

#### SELinux Policy Errors (Rocky/AlmaLinux)
```bash
# Check SELinux status
sestatus

# View SELinux denials
ausearch -m avc -ts recent

# Generate policy for common issues
audit2allow -a
```

#### UFW Configuration Issues (Ubuntu)
```bash
# Check UFW status
ufw status verbose

# Reset UFW if needed
ufw --force reset

# Check UFW logs
tail -f /var/log/ufw.log
```

#### Service Start Failures
```bash
# Check service status
systemctl status <service_name>

# View service logs
journalctl -u <service_name> --no-pager
```

### Log Locations
- **System logs**: `/var/log/messages` (RedHat) or `/var/log/syslog` (Ubuntu)
- **SSH logs**: `/var/log/secure` (RedHat) or `/var/log/auth.log` (Ubuntu)
- **Firewall logs**: `/var/log/firewalld` or `/var/log/ufw.log`
- **Fail2ban logs**: `/var/log/fail2ban.log`

## Integration with Other Playbooks

These OS-specific playbooks are designed to:
1. **Prepare systems** for role-based configuration
2. **Complement service roles** with OS-specific optimizations
3. **Provide baseline security** before application deployment

### Recommended Execution Order
1. OS-specific configuration (this documentation)
2. Service-specific roles (webserver, database, etc.)
3. Application deployment
4. Monitoring and backup setup

## Future Enhancements

Planned improvements include:
- Compliance reporting integration
- Automated vulnerability scanning
- Container runtime preparation
- Cloud-init integration
- Automated backup scheduling