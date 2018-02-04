openstack-bootstrap
===================

Create OpenStack instances and volume. also, attach and mount the volumes to the instances.

Requirements
------------

- Ansible 2.4 or higher. tested on 2.4.3.0 
- shade

You may need to ignore hostkey verification.

`~/.ansible.cfg` or equivalent path: 
```
[defaults]
host_key_checking = False
```

Role Variables
--------------

The role requires `openstack_bootstrap_specs` variable which contains list of dictionary variables. since this role is wrapping some Openstack module, you can use same structure of module variable.

- `name`: name of instance
- `ansible`: 
  - `inventory_groups`: Ansible inventory group name. The role generates the in-memory Ansible inventory. so that you can combine another roles with group name in your playbook without having actual inventory file.
  - `ssh_user`: ssh connection user.
  - `ssh_key`:  private key file path for ssh connection.
- `os_auth`: OpenStack auth options. this information will be used in `os_server`, `os_volume`, and `os_server_volume`. see [os_auth](http://docs.ansible.com/ansible/latest/os_auth_module.html) options for more details.
- `os_server`: Compute instance options. the `name` option of `os_server` will be replaced to `name`. see [os_server](http://docs.ansible.com/ansible/latest/os_server_module.html) for more details. 
- `volumes`: Volumes definition for compute instance. 
  - `name`: Name of OpenStack volume name. it will be used as `display_name` of `os_volume` and `volume` of `os_server_volume`.
  - `os_volume`: Volume option. see Openstack module [os_volume](http://docs.ansible.com/ansible/latest/os_volume_module.html) for more details.
  - `os_server_volume`: see Openstack module [os\_server_volume](http://docs.ansible.com/ansible/latest/os_server_volume_module.html) for more details.
  - `mount`:
    - `fstype`: Filesystem type.
    - `path`: Path to the mount point.


```yaml
openstack_bootstrap_specs:
  - name: instance-name-01
    ansible:
      inventory_groups:
        - group_name
      ssh_user: "ssh_user_name"
      ssh_key: "~/.ssh/your_private_key_file"
    os_auth:
      auth_url: "https://openstack-auth-url-dc-1"
      username: "your-name"
      password: "your-password"
      project_name: "your-project-01"
    os_server:
      key_name: "your-openstack-key-name"
      availability_zone: zone-a
      flavor: m1.large
      image: image-name-or-id
      security_groups:
        - security-group-name
      nics:
        - net-id: network-id
    volumes:
      - name: volume-name-01-01
        os_volume:
          volume_type: ceph
          size: 200
          availability_zone: nova
        os_server_volume:
          device: /dev/vdb
        mount:
          fstype: ext4
          path: /path/mount/01
      - name: volume-name-01-02
        os_volume:
          volume_type: ceph
          size: 200
          availability_zone: nova
        os_server_volume:
          device: /dev/vdc
        mount:
          fstype: ext4
          path: /path/mount/02
```

Dependencies
------------

This role is a wrapper for the standard Ansible Openstack module, so the following modules should be executable on the local machine. 

> http://docs.ansible.com/ansible/latest/list_of_cloud_modules.html#openstack

- os_auth
- os_server
- os\_server_volume
- os_volume


Example Playbook
----------------
Please set `connection: local` for your playbook.

    - hosts: localhost
      connection: local
      roles:
         - { role: openstack-bootstrap }

License
-------

MIT

Author Information
------------------

Sunggun Yu (<sunggun.dev@gmail.com>)
