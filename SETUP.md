# F5 BIG-IP Upgrade Automation - Setup Guide

This guide walks you through setting up the F5 BIG-IP 16→17 upgrade automation on your local machine from scratch.

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Step 1: Install Required Software](#step-1-install-required-software)
3. [Step 2: Clone the Repository](#step-2-clone-the-repository)
4. [Step 3: Install Ansible and F5 Collection](#step-3-install-ansible-and-f5-collection)
5. [Step 4: Configure Your Environment](#step-4-configure-your-environment)
6. [Step 5: Test Connection to F5](#step-5-test-connection-to-f5)
7. [Step 6: Run Pre-Upgrade Checks](#step-6-run-pre-upgrade-checks)
8. [Step 7: Perform Upgrade](#step-7-perform-upgrade)
9. [Step 8: Run Post-Upgrade Checks](#step-8-run-post-upgrade-checks)
10. [Troubleshooting](#troubleshooting)

---

## Prerequisites

Before starting, you need:

- ✓ A computer (Mac, Linux, or Windows with WSL)
- ✓ Git installed
- ✓ Python 3.7 or higher
- ✓ F5 BIG-IP device (version 16.x) with admin access
- ✓ Network connectivity to your F5 device
- ✓ Your F5 device IP address, username, and password
- ✓ F5 BIG-IP 17.x ISO file (location: server, /var/tmp/, or local)

---

## Step 1: Install Required Software

### For macOS:

#### 1.1 Install Homebrew (if not already installed)
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

#### 1.2 Install Git
bash
brew install git

#### 1.3 Install Python 3
bash
brew install python@3.11

#### 1.4 Verify installations
git --version
python3 --version

### For Linux (Ubuntu/Debian):
### 1.1 Update system packages
bash
sudo apt-get update
sudo apt-get upgrade -y

### 1.2 Install Git
bash
sudo apt-get install -y git

### 1.3 Install Python 3
bash
sudo apt-get install -y python3 python3-pip

### 1.4 Verify installations
bash
git --version
python3 --version

For Windows:
### 1.1 Install Git
Download from: https://git-scm.com/download/win

Run installer with default settings

### 1.2 Install Python 3
Download from: https://www.python.org/downloads/

IMPORTANT: Check "Add Python to PATH" during installation

Run installer

### 1.3 Verify installations (in PowerShell)

git --version
python --version

## Step 2: Clone the Repository

### 2.1 Open Terminal/PowerShell
macOS/Linux:
# Open Terminal

Windows:

powershell
# Open PowerShell (right-click → Run as Administrator)

2.2 Navigate to where you want to store the project
bash
# Example: Create a projects folder
cd ~
mkdir projects
cd projects
Windows PowerShell:

powershell
cd $env:USERPROFILE
mkdir projects
cd projects

2.3 Clone the repository
Replace YourUsername with your GitHub username:

bash
git clone https://github.com/YourUsername/f5-bigip-upgrade-16-to-17.git
cd f5-bigip-upgrade-16-to-17


2.4 Verify the repository structure
bash
# List all files
ls -la

# or on Windows PowerShell:
# Get-ChildItem -Force

You should see:

text
.
├── .gitignore
├── ansible.cfg
├── inventory/
│   └── hosts.ini
├── group_vars/
│   └── bigip.yml
├── playbooks/
│   ├── pre_upgrade_checks.yml
│   ├── upgrade_bigip.yml
│   └── post_upgrade_checks.yml
├── README.md
├── SETUP.md
└── requirements.yml

✓ Cloning complete!

## Step 3: Install Ansible and F5 Collection

3.1 Create a Python virtual environment (recommended)
This isolates your project dependencies:

macOS/Linux:

bash
python3 -m venv venv
source venv/bin/activate
Windows PowerShell:

powershell
python -m venv venv
.\venv\Scripts\Activate.ps1
You should see (venv) appear in your terminal prompt.

3.2 Upgrade pip
bash
pip install --upgrade pip
3.3 Install Ansible
bash
pip install ansible
3.4 Verify Ansible installation
bash
ansible --version
You should see output like:

text
ansible [core 2.14.x] ...

3.5 Install F5 collection from requirements file
bash
ansible-galaxy install -r requirements.yml
Or install manually:

bash
ansible-galaxy collection install f5networks.f5_modules
3.6 Verify F5 collection installation
bash
ansible-galaxy collection list | grep f5
You should see:

text
f5networks.f5_modules    X.XX.X
✓ Ansible and F5 collection installed!

## Step 4: Configure Your Environment

4.1 Create a local credentials file (DO NOT commit to GitHub)
Create a new file: inventory/hosts_local.ini

bash
# On macOS/Linux
touch inventory/hosts_local.ini

# On Windows PowerShell
New-Item -Path "inventory/hosts_local.ini" -ItemType File
4.2 Edit inventory/hosts_local.ini with your F5 details
Open the file with your text editor and add:

# LOCAL F5 DEVICE CONFIGURATION
# ⚠️  WARNING: This file contains credentials
# ⚠️  DO NOT COMMIT TO GITHUB (already in .gitignore)

[bigip]
# Replace with your actual F5 device details
bigip01 ansible_host=YOUR_F5_IP_ADDRESS ansible_user=admin ansible_password=YOUR_F5_PASSWORD

[bigip:vars]
ansible_network_os=f5networks.f5_modules
ansible_httpapi_use_ssl=yes
ansible_httpapi_validate_certs=no
ansible_httpapi_port=443
ansible_connection=httpapi

Example with real values:

text
[bigip]
bigip01 ansible_host=192.168.1.100 ansible_user=admin ansible_password=MyF5SecurePass123!

[bigip:vars]
ansible_network_os=f5networks.f5_modules
ansible_httpapi_use_ssl=yes
ansible_httpapi_validate_certs=no
ansible_httpapi_port=443
ansible_connection=httpapi


4.3 Update group_vars/bigip.yml with your settings
Open group_vars/bigip.yml and update:


# IMPORTANT: Update these values based on your environment

# Target BIG-IP version
bigip_version_target: "17.1.1.3"

# Choose ONE image source option:

# OPTION 1: HTTP server (comment out if not using)
image_source_type: "http"
bigip_software_url: "http://your-internal-server/BIGIP-17.1.1.3.0.0.6.iso"

# OPTION 2: Already on F5 device (uncomment if using)
# image_source_type: "local_f5"
# bigip_software_local_path: "/var/tmp/BIGIP-17.1.1.3.0.0.6.iso"

# OPTION 3: On your local machine (uncomment if using)
# image_source_type: "local_control_node"
# bigip_software_local_path: "/Users/YourName/Downloads/BIGIP-17.1.1.3.0.0.6.iso"

# Target boot volume
bigip_target_volume: "HD1.2"

4.4 Verify file permissions
bash
# Check .gitignore to ensure local files won't be committed
cat .gitignore | grep hosts_local

# You should see it's listed to prevent accidental commits
✓ Configuration complete!

## Step 5: Test Connection to F5
5.1 Test basic connectivity
bash
# Test connection to your F5
ansible all -i inventory/hosts_local.ini -m ping
Expected output (Success):
text
bigip01 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}

Troubleshooting connection issues:
Error: "Host unreachable"
# 1. Verify F5 IP is correct
# 2. Check network connectivity
ping YOUR_F5_IP_ADDRESS

# 3. Verify port 443 is open
telnet YOUR_F5_IP_ADDRESS 443

Error: "Authentication failed"

bash
# 1. Verify username in hosts_local.ini (usually 'admin')
# 2. Verify password is correct
# 3. Check if user account is locked on F5

5.2 Get F5 system information
bash
# Retrieve F5 device info
ansible bigip01 -i inventory/hosts_local.ini -m bigip_device_info -a "gather_subset=system-info"

You should see your F5 device information including current version.

✓ Connection successful!


## Step 6: Run Pre-Upgrade Checks
6.1 Run the pre-upgrade validation playbook
bash
ansible-playbook playbooks/pre_upgrade_checks.yml -i inventory/hosts_local.ini

What it checks:
✓ Device connectivity

✓ Current version is 16.x

✓ Disk space available

✓ Boot volume status

✓ HA configuration

✓ Virtual servers/pools

✓ Creates configuration backup

✓ Creates UCS backup

✓ Verifies license

✓ Checks system health

✓ Verifies ISO accessibility

Expected output:
text
TASK [Display pre-upgrade check header] **
ok: [bigip01] => {
    "msg": "=== PRE-UPGRADE VALIDATION CHECKS ===\n..."
}

TASK [Verify current version is 16.x] **
ok: [bigip01]

...

TASK [Display pre-upgrade checklist summary] **
ok: [bigip01] => {
    "msg": "=== PRE-UPGRADE VALIDATION CHECKLIST - SUMMARY ===\n✓ Device Connectivity: PASSED\n..."
}

6.2 Review the pre-upgrade report
bash
# Find and view the generated report
ls -la /tmp/f5_pre_upgrade_report_*.txt

# View the report
cat /tmp/f5_pre_upgrade_report_*.txt
If any checks FAIL:
Read the error message carefully

Check the "Troubleshooting" section below

Fix the issue

Re-run the pre-checks

Do NOT proceed to upgrade until all checks PASS

✓ Pre-upgrade checks complete!

## Step 7: Perform Upgrade
⚠️ IMPORTANT BEFORE PROCEEDING:
✓ All pre-upgrade checks PASSED

✓ Backups were created successfully

✓ You scheduled a maintenance window

✓ You notified stakeholders

✓ You verified F5 has sufficient disk space

✓ You have network access to the F5 device

7.1 Run the upgrade playbook
bash
# Start the upgrade
ansible-playbook playbooks/upgrade_bigip.yml -i inventory/hosts_local.ini -v

The -v flag shows verbose output so you can monitor progress.

What the upgrade does:
Gets current F5 version (16.x)

Creates pre-upgrade backup

Uploads/accesses ISO image

Installs software on target volume

Reboots F5 device

Waits for device to come back online (2-5 minutes)

Verifies new version

Displays success summary

Expected output:
text
TASK [Get current BIG-IP info] **
ok: [bigip01]

TASK [Display current version] **
ok: [bigip01] => {
    "msg": "Current version: 16.1.x"
}

TASK [Upload software image to BIG-IP] **
changed: [bigip01]

TASK [Reboot BIG-IP] **
changed: [bigip01]

TASK [Wait for device to come back online] **
ok: [bigip01]

TASK [Display new version] **
ok: [bigip01] => {
    "msg": "Upgrade complete! New version: 17.1.1.3"
}
7.2 Monitor the upgrade
During upgrade (2-5 minutes):

DO NOT close terminal or interrupt the process

DO NOT reboot F5 manually

DO NOT unplug network cables

Monitor the console output for progress

If upgrade FAILS:
Check error message

Log into F5 web console to check status

Review system logs: /var/log/ltm

See "Troubleshooting" section below

✓ Upgrade complete!

## Step 8: Run Post-Upgrade Checks

8.1 Run the post-upgrade validation playbook
bash
ansible-playbook playbooks/post_upgrade_checks.yml -i inventory/hosts_local.ini
What it verifies:
✓ Device is online and responsive

✓ New version is correct (17.x)

✓ Boot volume is correct

✓ License is valid

✓ Virtual servers are online

✓ Pools and members are healthy

✓ HA status (if applicable)

✓ System health

✓ Network interfaces

✓ System logs clean

✓ DNS resolution working

✓ NTP sync correct

✓ Backups accessible

Expected output:
text
TASK [Verify upgraded version] **
ok: [bigip01]

TASK [Display new version] **
ok: [bigip01] => {
    "msg": "New BIG-IP version: 17.1.1.3"
}

TASK [Display post-upgrade validation summary] **
ok: [bigip01] => {
    "msg": "=== POST-UPGRADE VALIDATION CHECKLIST - SUMMARY ===\n✓ Device Connectivity: PASSED\n✓ Version Verification: PASSED (17.1.1.3)\n..."
}
8.2 Review the post-upgrade report
bash
# Find and view the generated report
ls -la /tmp/f5_post_upgrade_report_*.txt

# View the report
cat /tmp/f5_post_upgrade_report_*.txt
8.3 Post-upgrade next steps:
✓ Verify application traffic is flowing correctly

✓ Test failover if HA is configured

✓ Monitor device for 24 hours

✓ Review backup files

✓ Document upgrade completion

✓ Notify stakeholders

✓ Upgrade process complete!

Troubleshooting
Connection Issues
Problem: "Host unreachable"

bash
# 1. Verify IP address
ping YOUR_F5_IP_ADDRESS

# 2. Check if F5 is up
curl -k https://YOUR_F5_IP_ADDRESS

# 3. Verify credentials
# Edit hosts_local.ini and double-check IP, username, password
Solution:

Verify F5 IP address in inventory/hosts_local.ini

Check network connectivity

Verify F5 device is powered on and responsive

Check if F5 HTTPS port (443) is blocked by firewall

Authentication Issues
Problem: "Authentication failed"

text
fatal: [bigip01]: FAILED! => {
    "msg": "Received 401 unauthorized: Unable to authenticate"
}
Solution:

Verify admin username in inventory/hosts_local.ini

Verify admin password is correct

Check if admin user account is locked on F5

Reset password on F5 web console if needed

Ensure you're using correct credentials

Image Upload Issues
Problem: "ISO file not found"

Solution:

Verify image path in group_vars/bigip.yml

If using local_f5, verify file exists on F5:

bash
# Log into F5 via SSH and check:
ls -lh /var/tmp/BIGIP-17.1.1.3.0.0.6.iso
If using local_control_node, verify file exists on your machine:

bash
# Check on your computer:
ls -lh ~/Downloads/BIGIP-17.1.1.3.0.0.6.iso
If using http, verify URL is accessible:

bash
curl -I http://your-server/BIGIP-17.1.1.3.0.0.6.iso
Disk Space Issues
Problem: "Insufficient disk space"

text
FATAL ERROR: Not enough disk space on BIG-IP
Required: 5GB
Available: 2GB
Solution:

Free up space on F5:

bash
# SSH into F5
ssh admin@YOUR_F5_IP

# Remove old ISO files
rm /var/tmp/BIGIP-*.iso

# Clear log files (if safe)
# Contact F5 support for guidance
Wait 30 minutes for cleanup

Re-run pre-upgrade checks

Reboot Timeout
Problem: "Device did not come back online"

text
fatal: [bigip01]: FAILED! => {
    "msg": "Timeout waiting for device to come back online"
}
Solution:

Wait 5-10 more minutes (upgrades can take time)

Check if F5 is online:

bash
ping YOUR_F5_IP_ADDRESS
curl -k https://YOUR_F5_IP_ADDRESS
Log into F5 web console and check upgrade status

Review F5 system logs for errors

Contact F5 support if device doesn't come online

License Issues
Problem: "License invalid after upgrade"

Solution:

Log into F5 web console

Go to System → License

Re-license the device if needed

Check if v16 license works with v17 (some versions don't)

See F5 documentation for v16→v17 license requirements

Roll Back to Previous Version
If something goes wrong, you can rollback:

8.1 Log into F5 web console
text
https://YOUR_F5_IP_ADDRESS
Username: admin
Password: (your password)
8.2 Go to System → Software Management → Boot Locations
Select the previous boot volume

Click "Reboot"

F5 will restart from the old version

8.3 Restore configuration from backup (if needed)
bash
# Log in via SSH
ssh admin@YOUR_F5_IP

# Restore from backup
tmsh load sys ucs /var/tmp/pre_upgrade_backup.ucs
Getting Help
Check system logs:

bash
# Via SSH on F5
tail -100 /var/log/ltm
tail -100 /var/log/messages
Check Ansible debug output:

bash
# Re-run with more verbosity
ansible-playbook playbooks/upgrade_bigip.yml -i inventory/hosts_local.ini -vvv
Check F5 status:

bash
# Via SSH on F5
tmsh show sys software status
tmsh show sys failover
Contact F5 Support:

https://f5.com/support

Provide system logs and upgrade details

Quick Reference - Common Commands
bash
# Activate virtual environment (macOS/Linux)
source venv/bin/activate

# Activate virtual environment (Windows)
.\venv\Scripts\Activate.ps1

# Test connection
ansible all -i inventory/hosts_local.ini -m ping

# Get F5 version
ansible bigip01 -i inventory/hosts_local.ini -m bigip_device_info -a "gather_subset=system-info"

# Run pre-upgrade checks
ansible-playbook playbooks/pre_upgrade_checks.yml -i inventory/hosts_local.ini

# Run upgrade
ansible-playbook playbooks/upgrade_bigip.yml -i inventory/hosts_local.ini

# Run post-upgrade checks
ansible-playbook playbooks/post_upgrade_checks.yml -i inventory/hosts_local.ini

# Run all playbooks together
ansible-playbook playbooks/pre_upgrade_checks.yml playbooks/upgrade_bigip.yml playbooks/post_upgrade_checks.yml -i inventory/hosts_local.ini

# View generated reports
cat /tmp/f5_pre_upgrade_report_*.txt
cat /tmp/f5_post_upgrade_report_*.txt
Next Steps
✓ Follow this setup guide step-by-step

✓ Test connection to your F5

✓ Run pre-upgrade checks

✓ Schedule upgrade window

✓ Run upgrade playbook

✓ Run post-upgrade checks

✓ Monitor for 24 hours

✓ Update documentation

Need Help?
If you encounter issues:

Check the Troubleshooting section above

Review Step 5: Test Connection to verify connectivity

Check Step 6 pre-upgrade check outputs

Review F5 system logs (/var/log/ltm)

Contact your F5 administrator or F5 support

Questions?
For questions about:

Ansible: See Ansible Documentation

F5 Modules: See F5 Ansible Collection Docs

BIG-IP Upgrade: See F5 BIG-IP Upgrade Guide

Good luck with your upgrade! 🚀

text

### 1.3 - Commit this file

- Scroll to **Commit changes**
- Message: `Add comprehensive setup guide for local machine configuration and upgrade workflow`
- Click **Commit changes**

***

## **STEP 2: Update README.md to link to SETUP.md**

### 2.1 - Edit README.md

- Click on **README.md**
- Click the **pencil icon** to edit

### 2.2 - Add this section near the top (after the Project Structure)

Find this line in the README:
```markdown
## ⚙️ Prerequisites
Add this BEFORE that section:

text
## 🚀 Quick Start

**New to this project?** Start here:

1. [Installation Guide](SETUP.md#step-1-install-required-software)
2. [Clone Repository](SETUP.md#step-2-clone-the-repository)
3. [Configure Environment](SETUP.md#step-4-configure-your-environment)
4. [Test Connection](SETUP.md#step-5-test-connection-to-f5)
5. [Run Upgrade](SETUP.md#step-7-perform-upgrade)

**Experienced users?** Jump to [Usage](#-usage) section below.

---
2.3 - Commit the update
Scroll to Commit changes

Message: Update README with quick start guide linking to SETUP.md

Click Commit changes

STEP 3: View your complete project
Your GitHub repository now has everything needed for local setup:

✅ Complete File Structure:

text
f5-bigip-upgrade-16-to-17/
├── README.md                       # Project overview + quick start
├── SETUP.md                        # ⭐ Complete setup guide
├── ansible.cfg                     # Ansible configuration
├── requirements.yml                # Dependencies
├── .gitignore                      # Security
├── inventory/
│   └── hosts.ini                   # Example inventory (public)
├── group_vars/
│   └── bigip.yml                   # Configuration variables
└── playbooks/
    ├── pre_upgrade_checks.yml      # 12-step validation
    ├── upgrade_bigip.yml           # Main upgrade process
    └── post_upgrade_checks.yml     # 15-step verification

STEP 4: What the SETUP.md guide covers
Section	Details
Prerequisites	What you need before starting

Step 1	Install Git, Python, dependencies (Mac/Linux/Windows)
Step 2	Clone GitHub repo to your machine
Step 3	Install Ansible & F5 collection
Step 4	Create local config with your F5 details
Step 5	Test connectivity to F5
Step 6	Run pre-upgrade checks
Step 7	Perform upgrade
Step 8	Run post-upgrade checks

Troubleshooting	10+ common issues and solutions
Quick Reference	All important commands
STEP 5: How users will follow this guide
User follows the workflow:
text
1. Clone repo from GitHub
   ↓
2. Run Step 1-3 (Install software)
   ↓
3. Run Step 4 (Configure environment)
   ↓
4. Run Step 5 (Test connection)
   ↓
5. Run Step 6 (Pre-upgrade checks) ← MUST PASS
   ↓
6. Run Step 7 (Perform upgrade) ← MAIN UPGRADE
   ↓
7. Run Step 8 (Post-upgrade checks) ← VERIFY SUCCESS
   ↓
8. Check Troubleshooting if issues ← IF NEEDED




