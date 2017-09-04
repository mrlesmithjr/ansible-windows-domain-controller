# ansible-windows-domain-controller

## Requirements

## Role Variables

```yaml
---
# defaults file for ansible-windows-domain-controller

windows_domain_controller_info:
  dns_domain_name: vagrant.local
  domain_admin_password: vagrant
  domain_admin_user: administrator@vagrant.local
  safe_mode_password: Vagrant@123!
  state: domain_controller
```

## Dependencies

## Example Playbook

```yaml
---
- hosts: domain_controllers
  roles:
    - role: ansible-windows-dns-server

- hosts: domain_controllers
  tasks:
    - name: Setting Primary Domain Controller
      set_fact:
        windows_primary_domain_controller: "{{ groups['domain_controllers'][0] }}"

- hosts: domain_controllers
  roles:
    - role: ansible-windows-domain
      when: inventory_hostname == windows_primary_domain_controller
  tasks:

- hosts: domain_controllers
  serial: 1
  tasks:
    - name: Creating DNS Group
      add_host:
        groups: dns_servers
        hostname: "{{ hostvars[inventory_hostname]['ansible_ssh_host'] }}"
      changed_when: false

- hosts: domain_controllers
  pre_tasks:
    - name: Waiting For DC
      pause:
        minutes: 5
      when: _windows_domain['changed']

    - name: Setting DNS Servers
      win_dns_client:
        adapter_names: "*"
        ipv4_addresses: "{{ groups['dns_servers'] }}"
  roles:
    - role: ansible-windows-domain-controller
  tasks:
```

## License

MIT

## Author Information
