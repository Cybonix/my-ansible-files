# Ansible Lab Management

This repository contains Ansible scripts for managing Linux lab VMs, primarily focusing on Rocky Linux, Ubuntu, and AlmaLinux distributions.

## Purpose

The main goals of this repository are:

1.  **Efficient Lab Management:** Provide a collection of reusable Ansible playbooks and roles to automate common administrative tasks on lab VMs.
2.  **Learning Ansible:** Serve as a practical environment for learning and experimenting with Ansible concepts and best practices.
3.  **Staying Current:** Keep up-to-date with emerging Ansible features and techniques.

## Directory Structure

*   `ansible.cfg`: Main Ansible configuration file. **Remember to update `remote_user` and `private_key_file` with your details.**
*   `inventory/`: Contains inventory files defining hosts and groups.
    *   `hosts.ini`: The main inventory file. **Update `ansible_user` and `ansible_ssh_private_key_file` in the `[all:vars]` section, and customize host definitions as needed.**
*   `playbooks/`: Contains Ansible playbooks.
    *   `install_vim.yml`: A sample playbook to install the `vim` package on all hosts.
    *   `os-specific/`: OS-specific configuration playbooks for Rocky Linux, Ubuntu, and AlmaLinux.
    *   `complex-workflows/`: Advanced deployment workflows like LAMP stack.
*   `roles/`: Contains Ansible roles (collections of tasks, handlers, variables, etc., for a specific purpose).
    *   `webserver/`: Apache/Nginx web server configuration role.
    *   `database/`: MySQL/MariaDB/PostgreSQL database server role.
    *   `monitoring/`: Xymon monitoring system role (planned).
    *   `dns/`: BIND DNS server role (planned).
*   `group_vars/`: Contains directories for each group defined in the inventory, allowing for group-specific variable definitions.
*   `docs/`: Comprehensive documentation for roles, playbooks, and workflows.

## New Features

### ✅ Implemented Roles
- **Web Server Role**: Supports both Apache and Nginx with SSL, security hardening, and virtual hosts
- **Database Role**: MySQL, MariaDB, and PostgreSQL with security configuration and user management
- **Monitoring Role**: Based on existing Xymon setup (planned)
- **DNS Role**: Based on existing BIND configuration (planned)

### ✅ OS-Specific Playbooks
- **Rocky Linux Configuration**: Enterprise security, SELinux, firewalld, and system hardening
- **Ubuntu Configuration**: UFW firewall, unattended upgrades, AppArmor, and system optimization
- **AlmaLinux Configuration**: Repository management, security updates, and compliance features

### ✅ Complex Workflows
- **LAMP Stack Deployment**: Complete Linux, Apache, MySQL/MariaDB, PHP stack with testing and security
- **Kubernetes Cluster with Monitoring**: RKE2 deployment with Xymon integration (planned)

### ✅ Enhanced Inventory Management
- Comprehensive host grouping by service type and OS
- Production, development, and staging environment groups
- Support for complex multi-tier applications

### ✅ Group Variables Structure
- OS-specific variables for Rocky Linux, Ubuntu, and AlmaLinux
- Service-specific configurations for web servers and databases
- Global settings for security, networking, and system management

## Getting Started

1.  **Clone the repository:**
    ```bash
    git clone <repository_url>
    cd <repository_name>
    ```
2.  **Install Ansible:** Follow the official Ansible installation guide for your operating system: [https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)
3.  **Configure SSH Access:**
    *   Ensure you have SSH key-based authentication set up for the VMs you want to manage.
    *   Update `ansible.cfg` with your `remote_user` and `private_key_file` path.
    *   Alternatively, update the `[all:vars]` section in `inventory/hosts.ini` with your `ansible_user` and `ansible_ssh_private_key_file`. Settings in `inventory/hosts.ini` can override those in `ansible.cfg`.
4.  **Customize Inventory:**
    *   Edit `inventory/hosts.ini` to define your lab VMs and group them appropriately. Update IP addresses and hostnames.
5.  **Set Up Vault for Sensitive Data:**
    ```bash
    # Create encrypted variables file
    ansible-vault create group_vars/all/vault.yml
    
    # Add database passwords, API keys, etc.
    vault_db_root_password: "your_secure_password"
    vault_lamp_db_password: "another_secure_password"
    ```

## Usage Examples

### Basic Operations
```bash
# Test connectivity to all hosts
ansible all -m ping

# Run the sample vim installation playbook
ansible-playbook playbooks/install_vim.yml

# Target specific hosts or groups
ansible-playbook playbooks/install_vim.yml -l rocky1.lab
ansible-playbook playbooks/install_vim.yml -l webservers
```

### OS-Specific Configuration
```bash
# Configure Rocky Linux systems
ansible-playbook playbooks/os-specific/rocky-linux-config.yml

# Configure Ubuntu systems with automatic updates
ansible-playbook playbooks/os-specific/ubuntu-config.yml -e "auto_update_enabled=true"

# Configure AlmaLinux systems
ansible-playbook playbooks/os-specific/almalinux-config.yml
```

### Service Deployment
```bash
# Deploy Apache web server
ansible-playbook -i inventory/hosts.ini site.yml --tags webserver -e "webserver_type=apache"

# Deploy MariaDB database
ansible-playbook -i inventory/hosts.ini site.yml --tags database -e "database_type=mariadb"

# Deploy complete LAMP stack
ansible-playbook playbooks/complex-workflows/lamp-stack-deployment.yml
```

### Advanced Workflows
```bash
# Deploy LAMP stack with custom settings
ansible-playbook playbooks/complex-workflows/lamp-stack-deployment.yml \
  -e "app_name=mystore" \
  -e "app_domain=mystore.example.com" \
  -e "php_version=8.2"

# Deploy to production environment
ansible-playbook playbooks/complex-workflows/lamp-stack-deployment.yml -l production
```

## Documentation

Comprehensive documentation is available in the `docs/` directory:

- **[Roles Documentation](docs/roles.md)**: Detailed information about webserver and database roles
- **[OS-Specific Playbooks](docs/os-specific-playbooks.md)**: Rocky Linux, Ubuntu, and AlmaLinux configurations
- **[Complex Workflows](docs/complex-workflows.md)**: LAMP stack and other advanced deployments

## Security Best Practices

1. **Use Ansible Vault** for all sensitive data (passwords, API keys)
2. **Enable SSH key authentication** and disable password authentication
3. **Configure firewalls** on all systems
4. **Use SSL/TLS** for web services
5. **Regular security updates** through automated playbooks
6. **Monitor logs** and set up alerting

## Contributing

Feel free to contribute by adding new playbooks, roles, or improving existing ones. Please:

1. Follow the existing code structure and naming conventions
2. Document new features in the `docs/` directory
3. Test playbooks in a development environment first
4. Use descriptive commit messages
5. Update this README when adding major features

## TODO ✅ COMPLETED

*   ✅ Develop roles for common services (e.g., web server, database server).
*   ✅ Create playbooks for OS-specific configurations.
*   ✅ Implement more complex scenarios and workflows.

## Future Roadmap

*   Implement monitoring role based on existing Xymon setup
*   Develop DNS role based on existing BIND configuration
*   Add automated testing with Molecule
*   Implement SSL certificate automation with Let's Encrypt
*   Create CI/CD pipeline examples
*   Add container orchestration workflows
*   Develop backup and disaster recovery playbooks
