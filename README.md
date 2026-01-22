F5 BIG-IP 16 → 17 Upgrade Automation

An Ansible-based automation framework for upgrading F5 BIG-IP systems from version 16.x to 17.x, including pre-checks, UCS backup, upgrade execution, and post-upgrade validation.

📁 Project Structure
f5-bigip-upgrade-16-to-17/
├── inventory/
│   └── hosts.ini.example        # Inventory template (copy to hosts.ini and fill in)
├── group_vars/
│   └── bigip.yml                # BIG-IP upgrade variables
├── playbooks/
│   ├── pre_upgrade_checks.yml   # Pre-upgrade validation (includes UCS backup)
│   ├── upgrade_bigip.yml        # Main upgrade playbook
│   ├── post_upgrade_checks.yml  # Post-upgrade verification
│   ├── test_connection.yml      # Simple connectivity test
│   └── check_volumes.yml        # Utility: show boot volumes
├── reports/                     # Generated pre-upgrade reports (gitignored)
├── ansible.cfg                  # Ansible configuration
├── requirements.yml             # Ansible collection dependencies
├── .gitignore                   # Files excluded from Git
├── README.md                    # Project overview
└── SETUP.md                     # Detailed setup instructions

🚀 Quick Start

New to this project? Follow these steps in order:

Installation & environment setup

Inventory configuration

Test device connectivity

Run pre-upgrade checks

Execute the upgrade

Perform post-upgrade validation

⚙️ Prerequisites

Python 3.12+ (recommended)

Ansible 2.15+ (tested with 2.15.13)

F5 Ansible collection: f5networks.f5_bigip (v3.14.0+)

Network access to BIG-IP management interfaces

BIG-IP 17.x ISO image

Existing BIG-IP system running 16.x

📦 Installation
1. Create and activate a virtual environment
python3.12 -m venv venv
source venv/bin/activate

2. Install Ansible
pip install ansible-core==2.15.13

3. Install required collections
ansible-galaxy collection install -r requirements.yml

📝 Inventory Setup

Copy the inventory template and populate it with your device details:

cp inventory/hosts.ini.example inventory/hosts.ini
vi inventory/hosts.ini


⚠️ Never commit your real hosts.ini to Git.
Only hosts.ini.example should be version-controlled.

🛠️ Usage
Test connection
ansible-playbook playbooks/test_connection.yml

Run pre-upgrade checks (includes UCS backup)
ansible-playbook playbooks/pre_upgrade_checks.yml


This will:

Validate software version, boot volumes, HA status, license, and health

Verify ISO accessibility

Create a UCS backup on each BIG-IP

Generate a detailed report in reports/

Run the upgrade
ansible-playbook playbooks/upgrade_bigip.yml


⚠️ Important:
The upgrade playbook must be customized for your environment (image name, volume, HA behavior).

Post-upgrade validation
ansible-playbook playbooks/post_upgrade_checks.yml

🛡️ Security & Best Practices

.gitignore protects credentials, virtual environments, and reports

Never commit real inventory files

Reports and logs remain local and are excluded from Git

📝 Notes

On macOS, set this before running Ansible:

export OBJC_DISABLE_INITIALIZE_FORK_SAFETY=YES


All playbooks use the modern f5networks.f5_bigip collection

HTTPAPI connection is used throughout

Pre-upgrade checks include UCS backup creation via REST API

📚 References

F5 BIG-IP Upgrade Documentation

F5 Ansible Collection Documentation
