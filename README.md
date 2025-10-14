# Ansible Playbook to deploy Canton Node

This Ansible playbook automates the deployment and configuration of Canton Node. It ensures that the necessary dependencies, configuration files, and services are properly set up and running.

## Table of Contents

- [Requirements](#requirements)
- [Prerequisites](#prerequisites)
- [Setup](#setup)
- [Variables](#variables)
- [Usage](#usage)

## Requirements

Before using this playbook, ensure the following requirements are met:

1. **Ansible version**: Make sure you have Ansible 2.15+ installed.
2. **SSH Access**: Passwordless SSH access to all target servers.
3. **Python**: Python 3.x installed on the control node and all target hosts.
4. **Privileges**: The user running the playbook must have sudo privileges on the target machines.

## Prerequisites

**Install HashiCorp Vault**

This playbook relies on HashiCorp Vault to securely retrieve sensitive files, such as validator and node keys. Follow the [HashiCorp Vault Installation Guide](https://developer.hashicorp.com/vault/tutorials/getting-started/getting-started-install) to set up Vault on your infrastructure.

**Note on Secrets Management**

The playbook dynamically retrieves secret environment file from HashiCorp Vault. The keys are expected to follow a structured path format:
`<environment>/<project>/<organization>/<type>/<file_name>`

This structure ensures easy organization and secure retrieval of secrets.

## Setup

### 1. Install Ansible

If Ansible is not installed, visit the official documentation for detailed instructions on how to install Ansible on various Linux distributions:

[Ansible Installation Guide](https://docs.ansible.com/ansible/latest/installation_guide/installation_distros.html)

### 2. Clone the repository

Clone this repository to your Ansible control node:

```bash
git clone https://github.com/encapsulate-xyz/canton-ansible.git
cd canton-ansible
```

### 3. Inventory

Define your target servers' IP address or DNS in the inventory folder, and select either `devnet.yml`, `mainnet.yml` or `testnet.yml` to update.

Example for devnet.yml

```yaml
---
# maintains devnet inventory grouped by project names
all:
  vars:
    env: devnet
  children:
    canton:
      hosts:
        participant.canton.devnet.encapsulate.xyz:
          type: participant
        app.canton.devnet.encapsulate.xyz:
          type: app
        wallet.canton.devnet.encapsulate.xyz:
          type: wallet
        cns.canton.devnet.encapsulate.xyz:
          type: cns
```

## Variables

This playbook allows customization through several variables. You can define these variables in the following locations:

- **`group_vars/all.yml`**: Contains all the source urls and configurations.
- **`group_vars/mainnet.yml`** or **`group_vars/testnet.yml`**: Contains version-specific variables.
- **`group_vars/vault.yml`**: Store secret variables, such as `onboarding secret`, in this file.

### Usage

1. First, install the dependencies:

  ```bash
   ansible-galaxy install -r requirements.yml
  ```

2. Create a `ansible_vault_password` file containing ansible-vault password

3. Then run the playbook:

  ```bash
  ansible-playbook setup_participant.yml -l participant.canton.devnet.encapsulate.xyz -i inventory/devnet.yml
  ```

4. Similarly to deploy other services

  ```bash
  ansible-playbook setup_app.yml -l app.canton.devnet.encapsulate.xyz -i inventory/devnet.yml
  ansible-playbook setup_wallet.yml -l wallet.canton.devnet.encapsulate.xyz -i inventory/devnet.yml
  ansible-playbook setup_cns_web.yml -l cns.canton.devnet.encapsulate.xyz -i inventory/devnet.yml
  ```

After you run the playbook, it will ask for confirmation, displaying all the variables and the IP address or DNS of the server you are going to deploy.

Example output:

```bash
TASK [Display environment being deployed to] ***************************************************************************************************
ok: [participant.canton.devnet.encapsulate.xyz] => {
    "msg": [
        "Deploying to Host: participant.canton.devnet.encapsulate.xyz",
        "Groups: ['canton']",
        "Project: canton",
        "Environment: devnet",
        "Type: app",
        "Version: v0.4.20",
        "Username: canton-app",
        "Service Name: canton-app",
        "Operating System: linux",
        "System Architecture: amd64"
    ]
}

TASK [Confirm deployment details] ********************************************************************************************************************
Pausing for 40 seconds
(ctrl+C then 'C' = continue early, ctrl+C then 'A' = abort)
ok: [participant.canton.devnet.encapsulate.xyz]

TASK [Please confirm again] ********************************************************************************************************************
ok: [participant.canton.devnet.encapsulate.xyz] => {
    "msg": [
        "Deploying to Host: participant.canton.devnet.encapsulate.xyz",
        "Project: canton",
        "Environment: devnet",
        "Type: app"
    ]
}

TASK [Confirm deployment details] **************************************************************************************************************
Pausing for 20 seconds
(ctrl+C then 'C' = continue early, ctrl+C then 'A' = abort)
ok: [participant.canton.devnet.encapsulate.xyz]
```
