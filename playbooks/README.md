# Playbooks – BIG-IP 16 → 17 Upgrade

This directory contains the Ansible playbooks used by **Solution 01 – BIG-IP 16 → 17 Upgrade**.

## Playbooks

- `test_connection.yml`  
  Verifies Ansible can reach the BIG-IP device over the REST API (HTTPAPI connection).

- `pre_upgrade_checks.yml`  
  Performs safety checks before upgrading, including software version checks, boot volume inspection, HA status, license/health verification, ISO accessibility, and UCS backup creation.

- `upgrade_bigip.yml`  
  Executes the software upgrade workflow using the configured ISO image and boot volume parameters. This playbook must be customized per environment (image path, volume, HA behavior).

- `post_upgrade_checks.yml`  
  Validates that the device is running the target 17.x version, that key services are healthy, and that HA status and configuration are consistent after the upgrade.

- `check_volumes.yml`  
  Utility playbook that prints information about existing boot volumes to help you plan and validate the upgrade target.

## Usage notes

These playbooks are intended to be run with the inventory and variables defined at the repository root. See the main `README.md` and `SETUP.md` for detailed instructions.
