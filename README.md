Role Name
=========

Simple iptables

Requirements
------------

Debian based distro

Role Variables
--------------

    - iptables_input:
        interface:
          net:

    - iptables_nat:
        interface:
          net:

Example Playbook
----------------

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


License
-------

BSD

Author Information
------------------

https://imbicile.pp.ru/
