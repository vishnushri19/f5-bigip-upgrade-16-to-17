# Solution 02 – BIG-IP Onboarding with Declarative Onboarding (DO)

This solution will provide production-ready examples for onboarding F5 BIG-IP devices using Declarative Onboarding (DO). It is intended to standardize and automate initial provisioning for new or rebuilt devices.

Planned capabilities:

- System settings (hostname, DNS, NTP, time zone)
- Network configuration (VLANs, Self IPs, routes)
- Licensing and provisioning of core modules
- High availability configuration (device trust, device group, sync/failover)
- Environment-specific parameterization via Ansible inventories and variables

Planned structure (subject to change):

- `do/` – Sample DO JSON declarations
- `ansible/` – Playbooks and roles to push DO declarations
- `examples/` – Example topologies and environment-specific parameter files
- `docs/` – Implementation notes and best practices

This folder is currently a placeholder and will be filled as Solution 02 is developed.
