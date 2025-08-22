# Ansible Roles Documentation

This document provides comprehensive information about the Ansible roles available in this repository.

## Overview

The following roles have been implemented to address the TODO items from the original README:

- **webserver**: Apache/Nginx web server configuration
- **database**: MySQL/MariaDB/PostgreSQL database server setup
- **monitoring**: Xymon monitoring system (planned)
- **dns**: BIND DNS server configuration (planned)

## Role: webserver

### Description
Configures Apache or Nginx web servers with security hardening, SSL support, and virtual hosts.

### Supported Web Servers
- Apache HTTP Server
- Nginx

### Variables

#### Required Variables
- `webserver_type`: Web server type (`apache` or `nginx`)
- `webserver_servername`: Primary server name/domain

#### Optional Variables
- `webserver_port`: HTTP port (default: 80)
- `webserver_ssl_port`: HTTPS port (default: 443)
- `webserver_document_root`: Document root path (default: /var/www/html)
- `webserver_enable_ssl`: Enable SSL/TLS (default: false)
- `webserver_configure_firewall`: Configure firewall rules (default: true)
- `webserver_deploy_default_page`: Deploy default index page (default: true)
- `webserver_remove_default`: Remove default site (default: true)

### Usage Examples

#### Basic Apache Setup
```yaml
- hosts: webservers
  become: yes
  roles:
    - role: webserver
      vars:
        webserver_type: apache
        webserver_servername: example.com
        webserver_document_root: /var/www/example
```

#### Nginx with SSL
```yaml
- hosts: webservers
  become: yes
  roles:
    - role: webserver
      vars:
        webserver_type: nginx
        webserver_servername: secure.example.com
        webserver_enable_ssl: true
        webserver_configure_firewall: true
```

### Files Structure
```
roles/webserver/
├── defaults/main.yml      # Default variables
├── handlers/main.yml      # Service handlers
├── tasks/main.yml         # Main tasks
├── templates/
│   ├── apache_vhost.conf.j2    # Apache virtual host
│   ├── nginx_site.conf.j2      # Nginx site config
│   └── index.html.j2            # Default page
└── vars/
    ├── RedHat.yml         # RedHat family variables
    └── Debian.yml         # Debian family variables
```

### Security Features
- Security headers configuration
- Server signature hiding
- SSL/TLS with modern ciphers
- Firewall integration
- File permissions hardening

## Role: database

### Description
Installs and configures database servers including MySQL, MariaDB, and PostgreSQL with security hardening.

### Supported Databases
- MySQL
- MariaDB
- PostgreSQL

### Variables

#### Required Variables
- `database_type`: Database type (`mysql`, `mariadb`, or `postgresql`)

#### Security Variables
- `database_root_password`: Root/admin password
- `database_remove_anonymous_users`: Remove anonymous users (default: true)
- `database_remove_test_database`: Remove test database (default: true)
- `database_disallow_root_remote`: Disable remote root login (default: true)

#### Configuration Variables
- `database_configure_firewall`: Configure firewall (default: true)
- `database_custom_config`: Apply custom configuration (default: true)
- `database_databases`: List of databases to create
- `database_users`: List of users to create

### Usage Examples

#### MariaDB with Custom Database
```yaml
- hosts: dbservers
  become: yes
  roles:
    - role: database
      vars:
        database_type: mariadb
        database_root_password: "{{ vault_db_root_password }}"
        database_databases:
          - name: webapp_db
            encoding: utf8mb4
            collation: utf8mb4_unicode_ci
        database_users:
          - name: webapp_user
            password: "{{ vault_webapp_password }}"
            privileges: "webapp_db.*:ALL"
            host: localhost
```

#### PostgreSQL Setup
```yaml
- hosts: dbservers
  become: yes
  roles:
    - role: database
      vars:
        database_type: postgresql
        database_root_password: "{{ vault_pg_password }}"
        database_databases:
          - name: myapp_db
            encoding: UTF8
        database_users:
          - name: myapp_user
            password: "{{ vault_myapp_password }}"
            database: myapp_db
```

### Files Structure
```
roles/database/
├── defaults/main.yml      # Default variables
├── handlers/main.yml      # Service handlers
├── tasks/
│   ├── main.yml          # Main tasks
│   ├── mysql.yml         # MySQL/MariaDB tasks
│   └── postgresql.yml    # PostgreSQL tasks
├── templates/
│   ├── my.cnf.j2         # MySQL client config
│   ├── mariadb.cnf.j2    # MariaDB server config
│   ├── postgresql.conf.j2 # PostgreSQL config
│   └── pg_hba.conf.j2    # PostgreSQL auth config
└── vars/
    ├── RedHat.yml         # RedHat family variables
    └── Debian.yml         # Debian family variables
```

### Security Features
- Root password protection
- Anonymous user removal
- Test database removal
- Remote root access disabled
- Firewall configuration
- SSL/TLS ready configuration

## Group Variables

### File Structure
```
group_vars/
├── all/main.yml           # Global variables
├── webservers/main.yml    # Web server group
├── dbservers/main.yml     # Database server group
├── rocky_linux/main.yml   # Rocky Linux specific
├── ubuntu_servers/main.yml # Ubuntu specific
├── alma_linux/main.yml    # AlmaLinux specific
└── k8s_nodes/main.yml     # Kubernetes nodes
```

### Key Variables by Group

#### all/main.yml
- Common packages for all systems
- Security settings
- SSH configuration
- Time and locale settings

#### webservers/main.yml
- Web server type preference
- PHP configuration
- SSL settings
- Security headers

#### dbservers/main.yml
- Database type preference
- Performance tuning
- Backup configuration
- Monitoring settings

### OS-Specific Variables
Each OS group contains variables specific to package names, service names, configuration paths, and OS-specific features.

## Best Practices

### Variable Management
1. Use `group_vars` for group-specific settings
2. Use `host_vars` for host-specific overrides
3. Store sensitive data in Ansible Vault
4. Document all custom variables

### Security
1. Always set strong passwords using Vault
2. Enable firewall configuration
3. Use SSL/TLS where applicable
4. Regularly update security configurations

### Testing
1. Test roles in development environment first
2. Use `--check` mode for dry runs
3. Validate configurations after deployment
4. Monitor logs for errors

### Performance
1. Tune database settings for your workload
2. Configure appropriate caching
3. Monitor resource usage
4. Scale horizontally when needed

## Troubleshooting

### Common Issues

#### Web Server Won't Start
- Check configuration syntax
- Verify port availability
- Review firewall settings
- Check SELinux/AppArmor policies

#### Database Connection Issues
- Verify user credentials
- Check network connectivity
- Review firewall rules
- Validate host-based authentication

#### Permission Problems
- Check file ownership
- Verify directory permissions
- Review SELinux contexts (RedHat)
- Check AppArmor profiles (Ubuntu)

### Log Locations
- **Apache**: `/var/log/apache2/` (Debian) or `/var/log/httpd/` (RedHat)
- **Nginx**: `/var/log/nginx/`
- **MySQL/MariaDB**: `/var/log/mysql/` or `/var/log/mariadb/`
- **PostgreSQL**: `/var/log/postgresql/`

## Next Steps

1. Implement monitoring role based on existing Xymon setup
2. Develop DNS role based on existing BIND configuration
3. Create additional complex workflows
4. Add automated testing with Molecule
5. Implement SSL certificate automation with Let's Encrypt