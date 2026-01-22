F5 BIG-IP 16 to 17 Upgrade Automation
Ansible-based automation for upgrading F5 BIG-IP systems from version 16.x to 17.x.

📁 Project Structure
f5-bigip-upgrade-16-to-17/
├── inventory/
│   ├── hosts.ini.example      # Inventory template (copy to hosts.ini and fill in)
├── group_vars/
│   └── bigip.yml              # BIG-IP upgrade variables
├── playbooks/
│   ├── pre_upgrade_checks.yml # Pre-upgrade validation (with UCS backup)
│   ├── upgrade_bigip.yml      # Main upgrade playbook
│   ├── post_upgrade_checks.yml# Post-upgrade verification
│   ├── test_connection.yml    # Simple connection test
│   └── check_volumes.yml      # Utility: show boot volumes
├── reports/                   # Generated pre-upgrade reports (gitignored)
├── ansible.cfg                # Ansible configuration
├── requirements.yml           # Ansible dependencies
├── .gitignore                 # Files to exclude from Git
├── README.md                  # Project documentation
└── SETUP.md                   # Detailed setup instructions
🚀 Quick Start
New to this project? Start here:

Installation Guide
Clone Repository
Configure Environment
Test Connection
Run Pre-Upgrade Checks
Run Upgrade
⚙️ Prerequisites
Python 3.12+ (recommended)
Ansible 2.15+ (tested with 2.15.13)
F5 Ansible collection: f5networks.f5_bigip (v3.14.0+)
Network access to F5 BIG-IP devices
BIG-IP software image ISO file (version 17.x)
Existing BIG-IP system running version 16.x
📦 Installation
1. Create and Activate a Python Virtual Environment
python3.12 -m venv venv
source venv/bin/activate
2. Install Ansible
pip install ansible-core==2.15.13
3. Install Required Collections
ansible-galaxy collection install -r requirements.yml
📝 Inventory Setup
Copy the template and fill in your device details:
cp inventory/hosts.ini.example inventory/hosts.ini
vi inventory/hosts.ini
Never commit your real hosts.ini to git!
🛠️ Usage
Test Connection
ansible-playbook playbooks/test_connection.yml
Run Pre-Upgrade Checks (with UCS backup)
ansible-playbook playbooks/pre_upgrade_checks.yml
This will:
Validate version, boot volumes, HA, license, health, ISO accessibility
Create a UCS backup on each BIG-IP device
Generate a detailed report in reports/
Run the Upgrade
ansible-playbook playbooks/upgrade_bigip.yml
(Upgrade playbook must be customized for your environment.)
Post-Upgrade Validation
ansible-playbook playbooks/post_upgrade_checks.yml
🛡️ Security & Best Practices
Your .gitignore protects credentials, venv, and reports.
Only share hosts.ini.example (never your real inventory).
Reports and logs are not committed to git.
📝 Notes
On macOS, set this environment variable before running Ansible:
export OBJC_DISABLE_INITIALIZE_FORK_SAFETY=YES
All playbooks use the modern f5networks.f5_bigip collection and httpapi connection.
Pre-upgrade checks include UCS backup creation via REST API.
📚 References
F5 BIG-IP Upgrade Documentation
F5 Ansible Collection Docs
