# Change Log of [`udelarinterior.firewall_proxmox` role](https://github.com/UdelaRInterior/ansible-role-firewall-proxmox)

## [v3.0.1](https://github.com/UdelaRInterior/ansible-role-firewall-proxmox/tree/v3.0.1)

Thanks to [@exodious](https://github.com/exodious): fixed bug when running on multiple hosts with `pve_firewall_configure_cluster` set to true, FileNotFoundError was generated intermittently during cluster.fw copy task. ([#3](https://github.com/UdelaRInterior/ansible-role-firewall-proxmox/issues/3)) ([#4](https://github.com/UdelaRInterior/ansible-role-firewall-proxmox/pull/4))

## [v3.0.0](https://github.com/UdelaRInterior/ansible-role-firewall-proxmox/tree/v3.0.0)

Updated to Ansible v2.10 (use of collections) and several improvements:
* The variable `pve_hostname` is now available to use custom hostnames and provide more versatility than the old form inferred from inventory.
* To prevent unwanted impacts and security issues on a cluster in production, the cluster configuration is no longer configured by default.
* Now, using `modification_time` and `access_time` parameters in the `ansible.builtin.file` module, the complete idempotency of the role is duly represented in the tasks results.
* Proxmoxer is not required for the tasks of this role. The tasks that ensured that this false dependency was installed were removed.
* Full implementation of `pve_*` namespace usage, consistent with other Proxmox roles.
* This role configures the Proxmox firewall for both virtual machines and containers. However, many comments, task and variable names referred only to LXC containers. These details have been corrected to reflect the true scope of the role and to avoid confusion.
* Numerous variables renamed to be mnemonic.

## [v2.0.0](https://github.com/UdelaRInterior/ansible-role-firewall-proxmox/tree/v2.0.0)

* Adopt `pve_*` namespace for `api_host` variables, shared with `udelarinterior.proxmox_create_kvm` and `udelarinterior.proxmox_create_lxc`.

## [v1.0.0](https://github.com/UdelaRInterior/ansible-role-firewall-proxmox/tree/v1.0.0)

* First stable version. Configures proxmox firewall for containers and VMs.