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
*   `roles/`: Contains Ansible roles (collections of tasks, handlers, variables, etc., for a specific purpose).
*   `group_vars/`: Contains directories for each group defined in the inventory, allowing for group-specific variable definitions.

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
    *   To test your setup, you can run the sample `install_vim.yml` playbook:
        ```bash
        ansible-playbook playbooks/install_vim.yml
        ```
    *   If you want to target a specific host or group, use the `-l` or `--limit` flag:
        ```bash
        ansible-playbook playbooks/install_vim.yml -l rocky1.lab
        ansible-playbook playbooks/install_vim.yml -l webservers
        ```

## Contributing

Feel free to contribute by adding new playbooks, roles, or improving existing ones.

## TODO

*   Develop roles for common services (e.g., web server, database server).
*   Create playbooks for OS-specific configurations.
*   Implement more complex scenarios and workflows.
