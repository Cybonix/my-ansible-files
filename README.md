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
    *   `apply_common_configs.yml`: Applies the `common` and `ssh_hardening` roles to all targeted hosts.
*   `roles/`: Contains Ansible roles.
    *   `common/`: Configures common settings like timezone, essential packages, and MOTD.
    *   `ssh_hardening/`: Applies SSH security best practices.
*   `group_vars/`: Contains YAML files with variables for host groups.
    *   `all.yml`: Variables that apply to all hosts. You can override defaults for the `common` and `ssh_hardening` roles here.
*   `host_vars/`: (Directory not created by default but can be used for host-specific variables).

## Core Roles and Configuration

### `common` Role
This role applies baseline configurations to all servers. Key features:
*   Sets the system timezone (default: `Etc/UTC`).
*   Installs a list of common utility packages (e.g., `vim`, `git`, `htop`, `ncdu`).
*   Installs `epel-release` on RedHat family systems by default.
*   Deploys a dynamic MOTD script to `/etc/profile.d/motd.sh`.

**Key Variables (`roles/common/defaults/main.yml`, can be overridden in `group_vars/all.yml` or other group/host vars):**
*   `common_timezone`: System timezone (e.g., "America/New_York").
*   `common_packages_redhat`: List of packages for RedHat family OS.
*   `common_packages_debian`: List of packages for Debian family OS.
*   `common_install_epel_release`: Boolean, whether to install EPEL repository on RedHat systems (default: `true`).
*   `common_motd_extra_message`: An additional custom message to display in the MOTD.

### `ssh_hardening` Role
This role enhances SSH security based on common best practices.
*   Disables root login (`PermitRootLogin no`).
*   Disables password authentication (`PasswordAuthentication no`), enforcing key-based auth.
*   Allows configuration of the SSH port (default: `22`).
    *   **Warning:** Changing the port requires firewall updates and client adjustments. The role attempts to manage SELinux rules for the new port on RedHat systems.
*   Configures client keep-alive settings.
*   Allows restricting SSH access via `AllowUsers` and `AllowGroups`.

**Key Variables (`roles/ssh_hardening/defaults/main.yml`, can be overridden in `group_vars/all.yml` or other group/host vars):**
*   `ssh_port`: The SSH daemon port (default: `22`).
*   `ssh_permit_root_login`: Set to "no" to disable direct root login (default: "no").
*   `ssh_password_authentication`: Set to "no" to disable password login (default: "no").
*   `ssh_client_alive_interval`: Interval in seconds for client keep-alive messages.
*   `ssh_client_alive_count_max`: Number of keep-alive messages before disconnecting.
*   `ssh_x11_forwarding`: Whether to allow X11 forwarding (default: "no").
*   `ssh_max_auth_tries`: Maximum authentication attempts (default: 3).
*   `ssh_login_grace_time`: Time server waits for successful login (default: "30s").
*   `ssh_allow_agent_forwarding`: Whether to allow SSH agent forwarding (default: "yes").
*   `ssh_use_pam`: Whether to use PAM (default: "yes").
*   `ssh_allow_users`: List of usernames allowed to SSH (e.g., `["user1", "user2"]`).
*   `ssh_allow_groups`: List of group names allowed to SSH (e.g., `["ssh_users"]`).

### `group_vars/all.yml`
This file is intended for variables that should apply to all hosts in your inventory. You can override the default variables from the `common` and `ssh_hardening` roles here. For example:
```yaml
# group_vars/all.yml
common_timezone: "America/Chicago"
ssh_port: 2222
ssh_allow_users:
  - your_admin_user
```

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
5.  **Run a Sample Playbook:**
    *   To test your setup, you can run the sample `install_vim.yml` playbook or the new `apply_common_configs.yml` playbook:
        ```bash
        ansible-playbook playbooks/install_vim.yml
        # OR
        ansible-playbook playbooks/apply_common_configs.yml
        ```
    *   If you want to target a specific host or group, use the `-l` or `--limit` flag:
        ```bash
        ansible-playbook playbooks/apply_common_configs.yml -l rocky1.lab
        ```
    *   To run only specific roles from the `apply_common_configs.yml` playbook, use tags:
        ```bash
        # Apply only common configurations
        ansible-playbook playbooks/apply_common_configs.yml --tags "common"

        # Apply only SSH hardening
        ansible-playbook playbooks/apply_common_configs.yml --tags "ssh_hardening"
        ```
    *   **Important:** If you use the `ssh_hardening` role and change the `ssh_port`, subsequent Ansible connections will need to know the new port. You can specify this in your inventory:
        ```ini
        # inventory/hosts.ini example
        your_host ansible_host=192.168.1.X ansible_user=your_user ansible_port=2222
        ```
        Or by setting `ansible_port` in `group_vars` or `host_vars`.

## Contributing

Feel free to contribute by adding new playbooks, roles, or improving existing ones.

## TODO

*   Develop roles for common services (e.g., web server, database server).
*   Create playbooks for OS-specific configurations.
*   Implement more complex scenarios and workflows.
