Ansible Role: firewall_proxmox
=========

An Ansible role to enable and setting on our cluster instance, virtual machines and containers, the firewall service provided by Proxmox.

We can define through files, the rules that we want to apply to each host independently. In case of running the playbook in a host for which no rules were defined, a set of default rules that enable HTTP, HTTPS, SSH and ping traffic will be established.

[Proxmox Firewall Wiki](https://pve.proxmox.com/wiki/Firewall)


Requirements
------------

You must act on a Proxmox node or cluster already configured, i.e. you need Proxmox Virtual Environment (PVE) node already installed (tested with PVE 5), and a Proxmox user with LXC/KVM creation rights.

Yo also need an ssh key configured in the local machine, where ansible is ran, i.e. a file `~/.ssh/id_rsa.pub`.

Additionally, note that the firewall must be enabled in the network interface (s) of the host in which you want it to work.


Installation
------------

To be able to update later and eventually to modify it, prefer using `requirements.yml` with the git source:

```yaml
  ### From GitHub
- name: firewall_proxmox
  src: https://github.com/UdelaRInterior/ansible-role-firewall-proxmox.git
  scm: git
```

And then download it with `ansible-galaxy`:

```shell
$ ansible-galaxy install -r requirements.yml -g
```

Using `git`, you'll have to be carefull to folder name :

```shell
$ git clone https://github.com/UdelaRInterior/ansible-role-firewall-proxmox.git firewall_proxmox
```

Role Variables
--------------

The `defaults` variables define our cluster node (pve_api_host) and the location of firewall configuration files. To be specified by host under `files/firewall_proxmox/host_name/host_fqdn.fw`

```yaml
## Indicates if the cluster firewall will also be configured.
## Keep in mind that modifications to cluster configurations can affect many hosts,
## especially if groups are defined. Use with care
firewall_proxmox_configure_cluster: true

## Our Proxmox Virtual Enviroment node
pve_api_host: my_node.my_cluster.org

## Route to the lxc container correspondent .fw file. Relative to ansible project 'files' folder.
project_lxc_firewall_file: "firewall_proxmox/{{ inventory_hostname }}/{{ inventory_hostname.split('.')[0] }}.fw"

## Route to the lxc container correspondent .fw file. Absolute to the file on our local machine that runs the playbook.
local_path_to_lxc_firewall_file: "{{ playbook_dir }}/files/{{ project_lxc_firewall_file }}"
```

Dependencies
------------

We need ansible version >= 2.5 (?) to have the appropriate API of Proxmox modules. (Tested on Ansible 2.7)

Proxmox PVE 5 installed in a cluster node.


Example Playbook
----------------

Supposing that:
* `my_node.my_cluster.org` is our PVE node NS (api on port 8006)
* `my_node` it's PVE node name
* `deploy` our Proxmox user in this node
* `pve_containers_group` an Ansible group of the containers to define
* containers are named `<container>.my_node.my_cluster.org`
* container's firewall file can be found in `<playbook_path>/files/firewall_proxmox/<container>.my_node.my_cluster.org.fw`

Then our variables should be defined as follows:
```yaml
pve_api_host: my_node.my_cluster.org

project_lxc_firewall_file: "firewall_proxmox/{{ inventory_hostname }}.fw"

local_path_to_lxc_firewall_file: "{{ playbook_dir }}/files/{{ project_lxc_firewall_file }}"
```

Run the following playbook, enable the firewall for the cluster and configure (based on the .fw files) the desired rules for each host:

    - name: Enable and configure Proxmox firewall for the containers declared in pve_containers_group
      hosts: pve_containers_group
      remote_user: deploy
      become: yes
      gather_facts: no

      roles:
        - firewall_proxmox


License
-------

(c) Universidad de la República (UdelaR), Red de Unidades Informáticas de la UdelaR en el Interior. Licenced under GPL-v3

Author Information
------------------

[@santiagomr](https://github.com/santiagomr)
[@UdelaRInterior](https://github.com/UdelaRInterior)
https://proyectos.interior.edu.uy/
