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
            enp0s1:
              net:
                - 192.168.1.0/24 # Dwarves
                - 192.168.2.0/24 # Elves
            enp0s3:
              net:
                - 192.168.3.0/24 # Legion
                - 192.168.4.22/32 # Staseg

        - iptables_nat:
            enp0s1:
              net:
                - 192.168.10.0/24 # Home
            enp0s2:
              net:
                - 192.168.20.0/24 # Office
                - 192.168.21.0/24 # Haven
            enp0s3:
              net:
                - 192.168.30.0/24 # Hell
                - 192.168.31.0/24 # Valhalla
                - 192.168.32.0/24 # Helheim
                - 192.168.4.22/32 # Staseg

      roles:
        - role: imbicile.iptables
          tags: iptables
```

## Result -- cat /etc/iptables/rules.v4

```yaml
    #
    # Ansible managed
    #

    *filter
    :INPUT ACCEPT [0:0]
    :FORWARD ACCEPT [0:0]
    :OUTPUT ACCEPT [0:0]

    ### INPUT

    # Default
    -A INPUT -p icmp -m comment --comment "Allow Ping" -j ACCEPT
    -A INPUT -p tcp -m tcp --dport 22 -m comment --comment "Allow SSH" -j ACCEPT
    -A INPUT -m conntrack --ctstate RELATED,ESTABLISHED -m comment --comment "related and established connections" -j ACCEPT

    # ACCEPT
    -A INPUT -s 192.168.1.0/24 -i enp0s1 -j ACCEPT
    -A INPUT -s 192.168.2.0/24 -i enp0s1 -j ACCEPT
    -A INPUT -s 192.168.3.0/24 -i enp0s3 -j ACCEPT
    -A INPUT -s 192.168.4.22/32 -i enp0s3 -j ACCEPT

    # DROP
    -A INPUT -m comment --comment "Drop all input" -j DROP
    -A FORWARD -m comment --comment "Drop all forward" -j DROP
    COMMIT

    ### NAT
    *nat
    :PREROUTING ACCEPT [0:0]
    :INPUT ACCEPT [0:0]
    :POSTROUTING ACCEPT [0:0]
    :OUTPUT ACCEPT [0:0]

    # MASQUERADE
    -A POSTROUTING -s 192.168.10.0/24 -o enp0s1 -j MASQUERADE
    -A POSTROUTING -s 192.168.20.0/24 -o enp0s2 -j MASQUERADE
    -A POSTROUTING -s 192.168.21.0/24 -o enp0s2 -j MASQUERADE
    -A POSTROUTING -s 192.168.30.0/24 -o enp0s3 -j MASQUERADE
    -A POSTROUTING -s 192.168.31.0/24 -o enp0s3 -j MASQUERADE
    -A POSTROUTING -s 192.168.32.0/24 -o enp0s3 -j MASQUERADE
    -A POSTROUTING -s 192.168.4.22/32 -o enp0s3 -j MASQUERADE
    COMMIT
```

## License

BSD

## Author Information

https://imbicile.pp.ru/
