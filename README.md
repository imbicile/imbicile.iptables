# Role Name

Simple iptables

## Requirements

Debian based distro

## Role Variables

```yaml
- iptables_input:
    interface:
      net:

- iptables_nat:
    interface:
      net:
```

## Example Playbook

```yaml
---
- hosts:
    - all
  become: true

  vars:
    - iptables_input:
        eth0:
          net:
            - 192.168.1.0/24 # Dwarves
            - 192.168.2.0/24 # Elves
            - 192.168.3.0/24 # Hobbits
        eth1:
          net:
            - 192.168.13.0/24 # Legion
            - 192.168.4.22/32 # Staseg

    - iptables_nat:
        eth1:
          net:
            - 192.168.10.0/24 # Home
        eth2:
          net:
            - 192.168.20.0/24 # Office
            - 192.168.21.0/24 # Haven
        eth3:
          net:
            - 192.168.30.0/24 # Hell
            - 192.168.31.0/24 # Valhalla
            - 192.168.32.0/24 # Helheim
            - 192.168.14.22/32 # Staseg

  roles:
    - role: imbicile.iptables
      tags: iptables
```

## Result -- cat /etc/iptables/rules.v4

```bash
#
# Ansible managed
#

*filter
:INPUT ACCEPT [0:0]
:FORWARD ACCEPT [0:0]
:OUTPUT ACCEPT [0:0]

### INPUT

# Default
-A INPUT -i lo -m comment --comment "Accept lo interface" -j ACCEPT
-A INPUT -m state --state INVALID -m comment --comment "Drop invalid" -j DROP
-A INPUT -m conntrack --ctstate RELATED,ESTABLISHED -m comment --comment "Related and established connections" -j ACCEPT
-A INPUT -p icmp -m comment --comment "Allow Ping" -j ACCEPT
-A INPUT -p tcp -m tcp --dport 22 -m comment --comment "Allow SSH" -j ACCEPT

# ACCEPT
-A INPUT -s 192.168.1.0/24 -i eth0 -j ACCEPT
-A INPUT -s 192.168.2.0/24 -i eth0 -j ACCEPT
-A INPUT -s 192.168.3.0/24 -i eth0 -j ACCEPT
-A INPUT -s 192.168.13.0/24 -i eth1 -j ACCEPT
-A INPUT -s 192.168.4.22/32 -i eth1 -j ACCEPT
# DROP
-A INPUT -m comment --comment "Drop all input" -j DROP

### FORWARD
-A FORWARD -m conntrack --ctstate RELATED,ESTABLISHED -m comment --comment "Related and established connections" -j ACCEPT

# ACCEPT
-A FORWARD -s 192.168.10.0/24 -o eth1 -j ACCEPT
-A FORWARD -s 192.168.20.0/24 -o eth2 -j ACCEPT
-A FORWARD -s 192.168.21.0/24 -o eth2 -j ACCEPT
-A FORWARD -s 192.168.30.0/24 -o eth3 -j ACCEPT
-A FORWARD -s 192.168.31.0/24 -o eth3 -j ACCEPT
-A FORWARD -s 192.168.32.0/24 -o eth3 -j ACCEPT
-A FORWARD -s 192.168.14.22/32 -o eth3 -j ACCEPT
# DROP
-A FORWARD -m comment --comment "Drop all forward" -j DROP

COMMIT

### NAT
*nat
:PREROUTING ACCEPT [0:0]
:INPUT ACCEPT [0:0]
:POSTROUTING ACCEPT [0:0]
:OUTPUT ACCEPT [0:0]

# MASQUERADE
-A POSTROUTING -s 192.168.10.0/24 -o eth1 -j MASQUERADE
-A POSTROUTING -s 192.168.20.0/24 -o eth2 -j MASQUERADE
-A POSTROUTING -s 192.168.21.0/24 -o eth2 -j MASQUERADE
-A POSTROUTING -s 192.168.30.0/24 -o eth3 -j MASQUERADE
-A POSTROUTING -s 192.168.31.0/24 -o eth3 -j MASQUERADE
-A POSTROUTING -s 192.168.32.0/24 -o eth3 -j MASQUERADE
-A POSTROUTING -s 192.168.14.22/32 -o eth3 -j MASQUERADE
COMMIT
```

## License

BSD

## Author Information

https://imbicile.pp.ru/
