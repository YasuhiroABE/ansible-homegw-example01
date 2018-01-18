YasuhiroABE.homegw-example01
============================

This role is an example of my galaxy roles.
It aims to use as a boundary gateway box to manage my sub-network.
This is NOT an internet gateway or home router.

            <smtp relay>
                 |
                 |            +----------+---+----------+
     <internet>--+--<router>--|  nic0    |   |          |
              192.168.0.1/24  +--+-------+ A | iptables |
                 example.org  |  | nic1  | P | dnsmasq  |
                              |  ---+    | U | postfix  |
                    <switch>--| br0 |----| 2 | sshd     |
                 10.0.0.1/24  |  +--+    | C | cron-apt |
          subnet.example.org  |  | nic2  |   | ...      |
                              +--+-------+---+----------+

Requirements
------------

This role is tested on the following platforms.

### Ansible
- Version 2.4

### Distributions
- Ubuntu 16.04
- Debian 9

Role Variables
--------------

The 'external' means the network connected to a core network router.
All information will be defined by the network administrator.

The 'internal' means 

### Default
    # example: localhost
    hostname: ''
    
    # example: example.org
    external_domain: ''
    
    # example: 192.168.0.1/24
    external_hostprefix: ''
    
    # example: ens33
    external_eth_device: ''
    
    # example: 8.8.8.8
    external_nameservers: []

    # added an accept rule to iptables; example: [22,80]
    external_accept_tcp_ports: []
    
    # If set, gateway line will be added to the interfaces file.
    external_gateway: ''

    # list of external ntp servers ; example: [ntp1.jst.mfeed.ad.jp]
    external_ntp_servers: []

    # example: subnet.example.org
    internal_domain: ''
    
    # example: 10.0.0.1/24
    internal_hostprefix: ''

    # example: [ens34, ens35]
    internal_bridge_devices: []
    
    # example: 10.0.0.100
    internal_dhcp_iprange_from: ''
    
    # example: 10.0.0.200
    internal_dhcp_iprange_to: ''
    
    # example: smtp.example.org
    smtp_relayhost: ''
    
    # sshd listen on address
    sshd_listen_address: "{{ internal_hostprefix|ipaddr('address') }}"

Dependencies
------------

* YasuhiroABE.myfavorite-setting
* YasuhiroABE.homegw-network
* YasuhiroABE.homegw-dnsmasq 
* YasuhiroABE.homegw-iptables
* YasuhiroABE.homegw-postfix
* YasuhiroABE.homegw-openntpd

Example Playbook
----------------

    - hosts: servers
      vars:
        external_domain: example.org
        external_hostprefix: 172.16.0.1/24
        external_gateway: 172.16.0.1
        external_eth_device: ens33
        external_nameservers:
          - 8.8.8.8
        external_accept_tcp_ports:
          - 22
          - 80
        external_ntp_servers:
          - ntp1.jst.mfeed.ad.jp
          - ntp2.jst.mfeed.ad.jp
        hostname: homegw
        internal_domain: subnet.example.org
        internal_hostprefix: 192.168.10.1/24
        internal_bridge_devices:
          - ens34
          - ens35
        internal_dhcp_iprange_from: 192.168.10.200
        internal_dhcp_iprange_to: 192.168.10.240
        smtp_relayhost: smtp.example.org
        sshd_listen_address: "{{ external_hostprefix|ipaddr('address') }}"
      roles:
         - YasuhiroABE.homegw-example01

License
-------

Apache License 2.0

Author Information
------------------

[Yasuhiro ABE](http://www.yasundial.org/foaf.xml)
