Ansible Role: firewall_proxmox
=========

[![Galaxy](https://img.shields.io/badge/galaxy-UdelaRInterior.firewall__proxmox-blue.svg)](https://galaxy.ansible.com/udelarinterior/firewall_proxmox) ![GitHub tag (latest by date)](https://img.shields.io/github/v/tag/udelarinterior/ansible-role-firewall-proxmox?label=release&logo=github&style=social) ![GitHub stars](https://img.shields.io/github/stars/udelarinterior/ansible-role-firewall-proxmox?style=social) ![GitHub forks](https://img.shields.io/github/forks/udelarinterior/ansible-role-firewall-proxmox?style=social)

An Ansible role to enable and setting on our cluster instance, virtual machines and containers, the firewall service provided by Proxmox VE.

We can define through files, the rules that we want to apply to each host independently. In case of running the playbook in a host for which no rules were defined, a set of default rules that enable HTTP, HTTPS, SSH and ping traffic will be established.

- [Proxmox Firewall Documentation](https://pve.proxmox.com/wiki/Firewall).


Requirements
------------

You must act on a Proxmox Virtual Environment (PVE) node or cluster already configured. i.e. you need a PVE node already installed (tested with PVE 5 and 6) and a Proxmox user with LXC/KVM creation rights.

Yo also need an SSH key configured in the local machine, where Ansible is ran. i.e. a file `~/.ssh/id_rsa.pub`.

Additionally, note that the firewall must be enabled in the network interface(s) of the host in which you want it to work.


Installation
------------

```shell
$ ansible-galaxy install udelarinterior.firewall_proxmox
```

To be able to update later and eventually to modify it, prefer using `requirements.yml` with the git source:

```yaml
- name: udelarinterior.firewall_proxmox
  src: https://github.com/UdelaRInterior/ansible-role-firewall-proxmox.git
```
And then download it with `ansible-galaxy`:

```shell
$ ansible-galaxy install -r requirements.yml
```

Using `git`, you'll have to be carefull to folder name:

```shell
$ git clone https://github.com/UdelaRInterior/ansible-role-firewall-proxmox.git udelarinterior.firewall_proxmox
```

Role Variables
--------------

The `defaults` variables define our cluster node (`pve_api_host`) and the location of firewall configuration files.

A new interface was introduced in v3.0.0, with role's variables defined in the `pve_*` namespace when they are shared between several Proxmox roles, and in the `pve_firewall_*` namespace when they are specific to the present one.

```yaml
## Our Proxmox Virtual Enviroment node
pve_api_host: mynode.mycluster.org

# By default, we suppose that `inventory_hostname` is the FQDN or the hostname of the host to create, so we set the variable to the hostname.
# You can arbitrarly define this hostname
pve_hostname: "{{ inventory_hostname.split('.')[0] }}"

## Indicates if the cluster firewall will also be configured.
## Keep in mind that modifications to cluster configurations can
## affect many hosts, especially if groups are defined. Use with care
pve_firewall_configure_cluster: false

## Path to go to find firewall files
pve_firewall_configuration_files: "firewall_proxmox/etc/pve/firewall"

## Path to the host correspondent .fw file. Relative to Ansible project 'files' folder.
pve_firewall_host_configuration_file: "{{ pve_firewall_configuration_files }}/{{ pve_hostname }}.fw"

## Path to the host correspondent .fw file. Absolute to the file on our local machine that runs the playbook.
pve_firewall_path_to_host_configuration_file: "{{ playbook_dir }}/files/{{ pve_firewall_host_configuration_file }}"
```

Dependencies
------------

We need Ansible version >= 2.10 and a Proxmox Virtual Environment node or cluster version >= 5

Example Playbook
----------------

Supposing that:
* `mynode.mycluster.org` is our PVE node NS (API on port 8006)
* `mynode` it's PVE node name
* `deploy` our Proxmox user in this node
* `pve_containers_group` an Ansible group of the containers to define
* containers are named `<container>.mynode.mycluster.org`
* container's firewall file can be found in `<playbook_path>/files/firewall_proxmox/<container>.mynode.mycluster.org.fw`

Then our variables should be defined as follows:
```yaml
pve_api_host: mynode.mycluster.org

pve_firewall_host_configuration_file: "firewall_proxmox/{{ inventory_hostname }}.fw"

pve_firewall_path_to_host_configuration_file: "{{ playbook_dir }}/files/{{ pve_firewall_host_configuration_file }}"
```

Run the following playbook, enable the firewall for the cluster and configure (based on the .fw files) the desired rules for each host:
```yaml
  - name: Enable and configure Proxmox firewall for the containers declared in pve_containers_group
    hosts: pve_containers_group
    remote_user: deploy
    become: yes
    gather_facts: no

    roles:
      - udelarinterior.firewall_proxmox
```

License
-------

(c) Universidad de la República (UdelaR), Red de Unidades Informáticas de la UdelaR en el Interior. Licenced under GPL-v3

Author Information
------------------

[@santiagomr](https://github.com/santiagomr)
[@UdelaRInterior](https://github.com/UdelaRInterior)
https://proyectos.interior.edu.uy/
