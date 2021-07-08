# OpenShift DNS Setup

This playbook uses the role [**ansible-role-bind**](https://github.com/bertvv/ansible-role-bind) 
from Bert Van Vreckem to configure a minimal DNS server with a wildcard address 
on the preferred zone.
Its purpose is to provide a reliable resolution mechanism during OpenShift lab 
installations.

### Cloning:

This repository has submodules, hence recursive cloning is necessary:

```
$ git clone --recursive https://github.com/giannisalinetti/openshift-dns-setup.git
```

### Usage:

```
ansible-playbook playbook.yml
```

### Configure additional hosts:
To configure hosts in the zone edit the **vars/main.yaml** file:

```
bind_zone_master_server_ip: "{{ ansible_default_ipv4.address }}" 
bind_recursion: true
bind_zone_minimum_ttl: "2D"
bind_zone_ttl: "2W"
bind_zone_time_to_refresh: "2D"
bind_zone_time_to_retry: "2H"
bind_zone_time_to_expire: "2W"
bind_zone_domains:
  - name: 'extlab.io'
    name_servers:
      - "{{ ansible_hostname }}"
    hosts:
      # The ns master entry
      - name: "{{ ansible_hostname }}"
        ip: 
          - "{{ ansible_default_ipv4.address }}"
      # The wildcard address entry
      - name: "{{ wildcard_name }}"
        ip: 
          - "{{ wildcard_ipv4 }}"
      # Extra entries
      - name: host1
        ip:
          - 192.168.122.20
      - name: host2
        ip:
          - 192.168.122.21
      - name: host3
        ip:
          - 192.168.122.22
    networks:
      - "{{ network }}"
bind_forwarders:
  - "{{ upstream_dns }}"
bind_allow_query:
  - any
bind_listen_ipv4:
  - any
```

For the sake of readability the most common parameters like wildcard address/name,
forwarders, etc are defined in the main playbook file and recalled as internal
variables in the vars file.

```
    dns_zone: "extlab.io"
    wildcard_name: '*.apps'
    wildcard_ipv4: 192.168.122.220
    network: 192.168.122
    upstream_dns:
      - 192.168.122.1
```

