# Complex Workflows Documentation

This document covers the complex scenarios and workflows that address the TODO item for implementing more complex scenarios and workflows.

## Overview

Complex workflows combine multiple roles and services to deploy complete application stacks and infrastructure patterns. Currently implemented:

- **LAMP Stack Deployment**: Complete Linux, Apache, MySQL/MariaDB, PHP stack
- **Kubernetes Cluster with Monitoring**: RKE2 cluster deployment (planned)

## LAMP Stack Deployment

### Purpose
Deploys a complete LAMP (Linux, Apache, MySQL/MariaDB, PHP) stack with security hardening, monitoring, and testing.

### File Location
`playbooks/complex-workflows/lamp-stack-deployment.yml`

### Architecture
The workflow deploys across multiple phases:
1. **Database Server Setup**
2. **Web Server Configuration**
3. **Integration Testing**
4. **Security Hardening**

### Target Infrastructure
- **Database Servers**: Hosts in `dbservers` group or with 'db' in hostname
- **Web Servers**: Hosts in `webservers` group or with 'web' in hostname
- **Integration Group**: `lamp_stack` group for coordinated deployment

### Prerequisites

#### Required Vault Variables
```yaml
# Store in group_vars/lamp_stack/vault.yml or similar
vault_lamp_db_password: "secure_database_password"
```

#### Inventory Configuration
```ini
[lamp_stack]
web1.lab
db1.lab

[webservers]
web1.lab

[dbservers]
db1.lab
```

### Usage Examples

#### Basic LAMP Deployment
```bash
# Deploy complete LAMP stack
ansible-playbook playbooks/complex-workflows/lamp-stack-deployment.yml

# Deploy with custom application name
ansible-playbook playbooks/complex-workflows/lamp-stack-deployment.yml \
  -e "app_name=mystore" \
  -e "app_domain=mystore.example.com"

# Deploy with different PHP version
ansible-playbook playbooks/complex-workflows/lamp-stack-deployment.yml \
  -e "php_version=8.2"
```

#### Advanced Configuration
```bash
# Deploy with custom database settings
ansible-playbook playbooks/complex-workflows/lamp-stack-deployment.yml \
  -e "lamp_db_name=ecommerce_db" \
  -e "lamp_db_user=ecommerce_user"

# Deploy to specific environment
ansible-playbook playbooks/complex-workflows/lamp-stack-deployment.yml \
  -l production
```

### Key Variables

#### Application Configuration
- `app_name`: Application name (default: "webapp")
- `app_domain`: Application domain (default: "webapp.lab")
- `app_document_root`: Web document root (default: "/var/www/webapp")

#### Database Configuration
- `lamp_db_name`: Database name (default: "webapp_db")
- `lamp_db_user`: Database user (default: "webapp_user")
- `lamp_db_password`: Database password (uses vault variable)

#### PHP Configuration
- `php_version`: PHP version to install (default: "8.1")

### Deployment Phases

#### Phase 1: Database Server Setup
- Installs and configures MariaDB using the database role
- Creates application database and user
- Configures remote access if web and DB servers are separate
- Sets up security hardening

#### Phase 2: Web Server Configuration
- Installs Apache using the webserver role
- Installs PHP and required extensions
- Configures PHP settings for production
- Creates application directory structure
- Deploys sample application
- Installs Composer for dependency management

#### Phase 3: Integration Testing
- Waits for services to be ready
- Tests database connectivity
- Validates web server response
- Verifies application functionality

#### Phase 4: Security Hardening
- Configures Apache security headers
- Hides server version information
- Sets up PHP security settings
- Applies SELinux contexts (RedHat family)

### Sample Application

The workflow deploys a comprehensive PHP application that demonstrates:

#### Features
- Database connectivity testing
- System information display
- Service status monitoring
- PHP configuration overview
- Security validation

#### Application Structure
```
/var/www/webapp/
├── index.php          # Main application file
└── config/
    └── database.php   # Database configuration
```

#### Database Integration
- PDO-based database connections
- Prepared statement examples
- Error handling and logging
- Connection pooling ready

### Security Features

#### Apache Hardening
- Security headers (X-Content-Type-Options, X-Frame-Options, etc.)
- Server signature hiding
- Version information concealment
- SSL/TLS configuration ready

#### PHP Security
- Disabled dangerous functions
- Error display disabled in production
- Secure session configuration
- File upload restrictions

#### Database Security
- Restricted user privileges
- Connection encryption ready
- Audit logging enabled
- Backup user configuration

### Monitoring and Validation

#### Health Checks
- Service availability monitoring
- Database connection validation
- Web application response testing
- Performance metrics collection

#### Logging
- Apache access and error logs
- PHP error logging
- Database query logging
- System audit trails

### Customization Options

#### Template Customization
Modify templates in `playbooks/complex-workflows/templates/`:
- `lamp_sample_app.php.j2`: Main application
- `db_config.php.j2`: Database configuration

#### Role Variable Overrides
Override role defaults for specific requirements:
```yaml
# Custom webserver configuration
webserver_enable_ssl: true
webserver_configure_firewall: true

# Custom database settings
database_custom_config: true
database_max_connections: 300
```

## Kubernetes Cluster with Monitoring (Planned)

### Purpose
Deploys a complete RKE2 Kubernetes cluster with integrated monitoring using the existing RKE2 and Xymon components.

### Planned Architecture
1. **Cluster Bootstrap**: RKE2 master and worker nodes
2. **Network Configuration**: CNI and network policies
3. **Storage Setup**: Local path provisioner
4. **Monitoring Stack**: Xymon integration
5. **Security Hardening**: RBAC and pod security

### Target Implementation
- Extend existing `createRKE2Cluster.yml`
- Integrate Xymon monitoring
- Add cluster validation
- Implement backup strategies

## Best Practices for Complex Workflows

### Planning and Design

#### Infrastructure Requirements
1. **Resource Planning**: CPU, memory, storage requirements
2. **Network Design**: Port requirements, firewall rules
3. **Security Planning**: Certificate management, secrets handling
4. **Backup Strategy**: Data protection and recovery procedures

#### Workflow Design
1. **Modular Approach**: Use roles for reusable components
2. **Error Handling**: Implement comprehensive error checking
3. **Rollback Capability**: Plan for deployment failures
4. **Testing Integration**: Validate each deployment phase

### Execution Guidelines

#### Pre-deployment Checklist
- [ ] Inventory configuration validated
- [ ] Vault variables properly encrypted
- [ ] Network connectivity verified
- [ ] Resource requirements met
- [ ] Backup procedures in place

#### Deployment Process
1. **Dry Run**: Execute with `--check` flag
2. **Staged Deployment**: Deploy to development first
3. **Monitoring**: Watch logs during deployment
4. **Validation**: Run post-deployment tests
5. **Documentation**: Update deployment records

### Security Considerations

#### Secrets Management
- Use Ansible Vault for all sensitive data
- Rotate passwords regularly
- Implement least-privilege access
- Monitor secret access

#### Network Security
- Configure firewalls restrictively
- Use TLS/SSL for all communications
- Implement network segmentation
- Monitor network traffic

#### Application Security
- Regular security updates
- Vulnerability scanning
- Security header configuration
- Access logging and monitoring

## Troubleshooting Complex Workflows

### Common Issues

#### Database Connection Problems
```bash
# Test database connectivity
mysql -u lamp_user -p -h db_server -e "SELECT 1;"

# Check firewall rules
firewall-cmd --list-all

# Verify user permissions
mysql -u root -p -e "SELECT User, Host FROM mysql.user;"
```

#### Web Server Issues
```bash
# Check Apache status
systemctl status apache2  # Ubuntu
systemctl status httpd    # RedHat

# Test PHP functionality
php -m | grep mysql
php -v

# Check virtual host configuration
apache2ctl -S  # Ubuntu
httpd -S       # RedHat
```

#### SSL/TLS Problems
```bash
# Test SSL configuration
openssl s_client -connect server:443

# Check certificate validity
openssl x509 -in certificate.crt -text -noout

# Verify cipher suites
nmap --script ssl-enum-ciphers -p 443 server
```

### Performance Optimization

#### Database Tuning
- Monitor query performance
- Optimize buffer pool size
- Configure connection pooling
- Implement query caching

#### Web Server Optimization
- Enable compression
- Configure caching headers
- Optimize virtual host settings
- Monitor connection limits

#### Application Performance
- Enable PHP opcache
- Optimize session handling
- Implement content caching
- Monitor memory usage

## Future Enhancements

### Planned Workflows
1. **LEMP Stack**: Nginx + MySQL + PHP
2. **MEAN Stack**: MongoDB + Express + Angular + Node.js
3. **Microservices Platform**: Docker + Kubernetes + Service Mesh
4. **CI/CD Pipeline**: Jenkins + GitLab + Automated Testing

### Monitoring Integration
1. **Prometheus + Grafana**: Metrics collection and visualization
2. **ELK Stack**: Centralized logging and analysis
3. **Alerting**: Automated incident response
4. **Performance Monitoring**: APM integration

### Automation Enhancements
1. **Rolling Updates**: Zero-downtime deployments
2. **Auto-scaling**: Dynamic resource allocation
3. **Disaster Recovery**: Automated backup and restore
4. **Compliance**: Automated security and compliance checking