# F5 BIG-IP 16 to 17 Upgrade Automation

Ansible-based automation for upgrading F5 BIG-IP systems from version 16.x to 17.x

## 📋 Project Structure

f5-bigip-upgrade-16-to-17/
├── inventory/
│   └── hosts.ini              # BIG-IP device inventory
├── group_vars/
│   └── bigip.yml              # BIG-IP upgrade variables
├── playbooks/
│   ├── pre_upgrade_checks.yml # Pre-upgrade validation
│   ├── upgrade_bigip.yml      # Main upgrade playbook
│   └── post_upgrade_checks.yml# Post-upgrade verification
├── ansible.cfg                # Ansible configuration
├── requirements.yml           # Ansible dependencies
├── .gitignore                 # Files to exclude from Git
└── README.md                  # Project documentation

## 📁 Project Structure

```text
f5-bigip-upgrade-16-to-17/
├── inventory/
│   └── hosts.ini              # BIG-IP device inventory
├── group_vars/
│   └── bigip.yml              # BIG-IP upgrade variables
├── playbooks/
│   ├── pre_upgrade_checks.yml # Pre-upgrade validation
│   ├── upgrade_bigip.yml      # Main upgrade playbook
│   └── post_upgrade_checks.yml# Post-upgrade verification
├── ansible.cfg                # Ansible configuration
├── requirements.yml           # Ansible dependencies
├── .gitignore                 # Files to exclude from Git
└── README.md                  # Project documentation


## ⚙️ Prerequisites

- Ansible 2.9 or higher
- F5 Ansible collection: `f5networks.f5_modules`
- Network access to F5 BIG-IP devices
- BIG-IP software image ISO file (version 17.x)
- Existing BIG-IP system running version 16.x

## 📦 Installation

### 1. Install Ansible

```bash
pip install ansible

### 2. Install F5 Collection

ansible-galaxy collection install f5networks.f5_modules

Or install from requirements file:

ansible-galaxy install -r requirements.yml

