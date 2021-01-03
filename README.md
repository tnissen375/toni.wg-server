toni.wg-server
===========

Ansible role to install NFS over wireguard. Wireguard server and clients will be configured.

Todo: 
  - Multiple wireguard interfaces
  - Make NFS optional

Requirements
------------

Roles:
  - toni.wg-client
  - geerlingguy.nfs

Role Variables
--------------

```bash
wg_server_ip: 10.8.0.1   
wg_subnet: 10.8.0.0
wg_listen_port: "51820"
wg_interface: wg0
wg_dir: "/etc/wireguard"
wg_clients: "wg_clients"        # Inventory group where the clients are listed
nfs_server_dir: "/mnt/shared"   # Role is using NFS4, so this should be the root mountpoint 
```

Example Playbook to create a Wiregurad network
----------------------------------------------

```yaml
---
- name: Wireguard/NFS server setup
  hosts: wg_server
  become: yes
  vars:
    mode: "setup"
    nfs_exports:
      - "/mnt/scalelite-recordings       10.8.0.0/24(fsid=0,rw,sync,no_subtree_check,hide,no_root_squash)"
  roles:
    - wg-server
    - { role: geerlingguy.nfs }
 
- name: Wireguard/NFS client setup
  hosts: wg_clients
  become: yes
  vars:
     mode: "setup"
  roles:
    - wg-client

- name: Wireguard/NFS server startup
  hosts: wg_server
  become: yes
  vars:
    mode: "start"
  roles:
    - wg-server

- name: Wireguard/NFS client start
  hosts: wg_clients
  become: yes
  vars:
     mode: "start"
  roles:
     - wg-client
```

Example inventory hosts file
```yaml
[wg_server]
server.jidu.eu

[wg_clients]
bbb1.jidu.eu wg_client_ip=10.8.0.2
bbb2.jidu.eu wg_client_ip=10.8.0.3
front.jidu.eu wg_client_ip=10.8.0.4
turn.jidu.eu wg_client_ip=10.8.0.5
scalelite.jidu.eu wg_client_ip=10.8.0.6
```

License
-------

MIT

Author Information
------------------

T.Nissen