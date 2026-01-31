# Solution 01 – BIG-IP 16 → 17 Upgrade Automation

This solution contains the Ansible-based automation for upgrading F5 BIG-IP systems from version 16.x to 17.x with pre- and post-upgrade checks.

Over time, the existing playbooks and variables in the repository will be organized under this solution. For now, this folder serves as the logical home for all upgrade-related content.

Planned structure (subject to change):

- inventory templates
- group_vars for upgrade parameters
- playbooks for:
  - test connection
  - pre-upgrade checks (including UCS backup)
  - upgrade execution
  - post-upgrade validation
- sample reports and documentation links
