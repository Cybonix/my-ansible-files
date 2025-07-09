## Agent Instructions for Ansible Lab Management Repository

Welcome, AI Agent! This document provides guidelines for working on this repository, which focuses on creating Ansible scripts for managing Linux lab VMs (Rocky Linux, Ubuntu, AlmaLinux).

### Core Principles:

1.  **Idempotency:** All Ansible tasks, playbooks, and roles must be idempotent. Running them multiple times should result in the same system state without errors.
2.  **Modularity:** Favor roles for organizing reusable sets of tasks, variables, and handlers. Playbooks should primarily orchestrate these roles.
3.  **Clarity and Readability:**
    *   Use descriptive names for variables, tasks, roles, and playbooks.
    *   Comment complex tasks or non-obvious logic.
    *   Structure YAML files consistently with proper indentation.
4.  **OS Agnosticism (where practical):** When creating roles or tasks intended for multiple distributions, use Ansible facts (e.g., `ansible_os_family`, `ansible_distribution`) to handle differences. If a task is highly OS-specific, make that clear in its naming or documentation.
5.  **Security:**
    *   Avoid hardcoding sensitive information (passwords, API keys). Use Ansible Vault for encrypting secrets or encourage users to provide them via extra vars or environment variables. For this lab setup, we might initially use plaintext placeholders in `group_vars` or inventory, but clearly mark them as needing user replacement.
    *   Be mindful of permissions when creating files or directories.
6.  **Variable Management:**
    *   Default variables should be defined in `roles/your_role/defaults/main.yml`.
    *   Group-specific variables go into `group_vars/group_name.yml`.
    *   Host-specific variables go into `host_vars/hostname.yml` (though for this lab, we might rely more on inventory definitions).
    *   Clearly document variables that users are expected to override.
7.  **Testing (Conceptual):** While we might not have automated testing infrastructure initially:
    *   Always mentally "lint" your playbooks and roles.
    *   Test your changes against a representative VM (or multiple if OS differences are involved) before committing.
    *   The sample playbook `playbooks/install_vim.yml` can be a simple way to verify basic connectivity and execution.

### Specific Instructions:

1.  **Directory Structure:** Adhere to the established directory structure:
    *   `playbooks/`: For top-level playbooks.
    *   `roles/`: For all roles.
    *   `inventory/`: For inventory files.
    *   `group_vars/`: For group-specific variables.
    *   `host_vars/`: For host-specific variables (less common for this project initially).
2.  **Naming Conventions:**
    *   Playbooks: `verb_noun.yml` (e.g., `install_webserver.yml`, `configure_firewall.yml`).
    *   Roles: `linux_distribution_service` (e.g., `common`, `nginx`, `rocky_secure_ssh`). Use generic names if applicable across OSes (e.g., `common_tools`).
    *   Tasks within roles: Use descriptive names for tasks.
3.  **Committing Changes:**
    *   Follow standard Git commit message conventions: a short subject line (max 50 chars), a blank line, and a more detailed body if necessary.
    *   Reference the issue or task you are working on if applicable.
4.  **Updating Documentation:** If you add new roles, playbooks, or significant variables that users need to be aware of, update the `README.md` accordingly.
5.  **Handling OS Differences:**
    *   Use `ansible_facts` like `ansible_os_family` (e.g., "RedHat", "Debian") or `ansible_distribution` (e.g., "Rocky", "Ubuntu", "AlmaLinux") in `when` conditions or to include OS-specific task files.
    *   Example:
        ```yaml
        - name: Install package (RedHat family)
          ansible.builtin.yum:
            name: some_package
            state: present
          when: ansible_os_family == "RedHat"

        - name: Install package (Debian family)
          ansible.builtin.apt:
            name: some_package
            state: present
          when: ansible_os_family == "Debian"
        ```
6.  **Sample Playbooks/Roles:**
    *   The initial `playbooks/install_vim.yml` is a very basic example.
    *   When adding new functionality, consider if it should be a role. Small, standalone tasks can be playbooks.

### Before Submitting:

1.  **Verify Idempotency:** Mentally review or (better) re-run your playbook/role to ensure it's idempotent.
2.  **Check for Hardcoded Secrets:** Ensure no sensitive data is hardcoded.
3.  **Review `README.md`:** Does it need updates based on your changes?
4.  **Follow Plan:** Ensure all steps in your assigned plan have been completed.

By following these guidelines, we can build a robust, maintainable, and useful set of Ansible scripts for our lab environment. Thank you for your contribution!
