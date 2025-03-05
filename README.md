# README

This document provides an overview of the Ansible Vault Lab assignment. It explains how to set up the environment, use the playbooks, manage vault passwords, and run playbooks with different environment credentials.

---

## Setup Instructions

**System Updates & Package Installation:**  
• Update your system packages:  
&nbsp;&nbsp;&nbsp;&nbsp;`sudo apt update && sudo apt upgrade -y`  

• Install Python3 and pip:  
&nbsp;&nbsp;&nbsp;&nbsp;`sudo apt install -y python3 python3-pip`  

• Install Git:  
&nbsp;&nbsp;&nbsp;&nbsp;`sudo apt install -y git`  

• Install Ansible:  
&nbsp;&nbsp;&nbsp;&nbsp;`pip3 install ansible`  

• Install passlib (choose one):  
&nbsp;&nbsp;&nbsp;&nbsp;- Option A (via apt):  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`sudo apt install python3-passlib`  
&nbsp;&nbsp;&nbsp;&nbsp;- Option B (via pip in a virtual environment):  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`pip3 install passlib`

**Repository Structure:**  
Ensure your repository follows a structure similar to:

&nbsp;&nbsp;&nbsp;&nbsp;ansible-vault-lab/  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;├── group_vars/ (contains vault files and vars.yml)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;├── inventory/ (contains production.ini)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;├── playbooks/ (contains create_user.yml and configure_service.yml)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;├── README.md  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;└── requirements.txt


Place your vault and configuration files in `group_vars/all`, inventory files in `inventory`, and playbooks in `playbooks`.

---

## Usage Examples

**Clone the Repository:**  
• Clone the repository and navigate to the project directory:  
&nbsp;&nbsp;&nbsp;&nbsp;`git clone https://github.com/<your-username>/ansible-vault-lab.git`  
&nbsp;&nbsp;&nbsp;&nbsp;`cd ansible-vault-lab`

**Run the Playbooks Using Vault Password Prompt:**  
• To create a user:  
&nbsp;&nbsp;&nbsp;&nbsp;`ansible-playbook -i inventory/production.ini playbooks/create_user.yml --ask-vault-pass --become --ask-become-pass`  

• To configure the service:  
&nbsp;&nbsp;&nbsp;&nbsp;`ansible-playbook -i inventory/production.ini playbooks/configure_service.yml --ask-vault-pass --become --ask-become-pass`

**Run the Playbooks Using Environment-Specific Vaults:**  
• For the development environment:  
&nbsp;&nbsp;&nbsp;&nbsp;`ansible-playbook -i inventory/production.ini playbooks/create_user.yml --vault-id dev@prompt`  

• For the production environment:  
&nbsp;&nbsp;&nbsp;&nbsp;`ansible-playbook -i inventory/production.ini playbooks/configure_service.yml --vault-id prod@prompt`

---

## Description of Each Playbook

**create_user.yml:**  
This playbook creates a new system user with a hashed password. It references an unencrypted `vars.yml` file that imports sensitive variables from an encrypted vault file. The password is hashed at runtime using the `password_hash` filter, ensuring that plain text passwords are never directly used.

**configure_service.yml:**  
This playbook sets up a simple Python HTTP server. It creates a directory for the service, copies an `index.html` file into that directory, launches the HTTP server in the background, and logs the API key from the vault to demonstrate how to integrate vault secrets into service configuration.

---

## Instructions for Vault Password Management

Sensitive data such as user passwords, API keys, and database credentials are stored in encrypted vault files.  
• **Create a new vault file:**  
&nbsp;&nbsp;&nbsp;&nbsp;`ansible-vault create group_vars/all/vault.yml`  
When prompted, add entries like:  
&nbsp;&nbsp;&nbsp;&nbsp;`vault_user_password: "secure_password123"`  
&nbsp;&nbsp;&nbsp;&nbsp;`vault_api_key: "api_key_123456"`  
&nbsp;&nbsp;&nbsp;&nbsp;`vault_db_credentials:`  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`username: "db_admin"`  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`password: "db_pass_123"`

• **Edit an existing vault file:**  
&nbsp;&nbsp;&nbsp;&nbsp;`ansible-vault edit group_vars/all/vault.yml`

**Important:** Never commit your vault passwords to version control. For multiple environments (e.g., development vs. production), maintain separate vault files (e.g., `dev_vault.yml` and `prod_vault.yml`) and reference them accordingly when running playbooks.

---

## Examples of Running Playbooks with Different Environment Credentials

• **Default Mode (using the primary vault file):**  
&nbsp;&nbsp;&nbsp;&nbsp;`ansible-playbook -i inventory/production.ini playbooks/create_user.yml --ask-vault-pass --become --ask-become-pass`

• **Using Environment-Specific Vaults:**  
&nbsp;&nbsp;&nbsp;&nbsp;- For the development environment:  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`ansible-playbook -i inventory/production.ini playbooks/create_user.yml --vault-id dev@prompt`  
&nbsp;&nbsp;&nbsp;&nbsp;- For the production environment:  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`ansible-playbook -i inventory/production.ini playbooks/configure_service.yml --vault-id prod@prompt`

---
