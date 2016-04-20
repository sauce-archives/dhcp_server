# dhcp_server

[![License](https://img.shields.io/badge/license-New%20BSD-blue.svg?style=flat)](https://raw.githubusercontent.com/saucelabs-ansible/dhcp_server/master/LICENSE)
[![Build Status](https://travis-ci.org/saucelabs-ansible/dhcp_server.svg?branch=master)](https://travis-ci.org/saucelabs-ansible/dhcp_server)

[![Platform](http://img.shields.io/badge/platform-ubuntu-dd4814.svg?style=flat)](#)

[![Project Stats](https://www.openhub.net/p/saucelabs-ansible-dhcp_server/widgets/project_thin_badge.gif)](https://www.openhub.net/p/saucelabs-ansible-dhcp_server/)

Ansible role to install and configure a [ISC DHCP](http://www.isc.org/downloads/dhcp/) server.


## Tests

| Family | Distribution | Version | Test Status |
|:-:|:-:|:-:|:-:|
| Debian | Ubuntu  | Precise | [![x86_64](http://img.shields.io/badge/x86_64-passed-006400.svg?style=flat)](#) |
| Debian | Ubuntu  | Trusty  | [![x86_64](http://img.shields.io/badge/x86_64-passed-006400.svg?style=flat)](#) |


## Requirements

- ansible >= 1.9.4


## Role Variables

- **debug**: flag to run debug tasks (default: false).
- **dhcp_server_classes**: class/subclass section of the dhcpd.conf file.
- **dhcp_server_dir_conf**: path to directory that will hold the dhcpd.conf file.
- **dhcp_server_dir_pid**: path to directory that will hold the dhcpd.pid file.
- **dhcp_server_hosts**: host section of the dhcpd.conf file.
- **dhcp_server_interfaces**: interfaces the server should serve DHCP requests (mandatory).
- **dhcp_server_keys**: key section of the dhcpd.conf file.
- **dhcp_server_options**: global options of the dhcpd.conf file.
- **dhcp_server_zones**: zone section of the dhcpd.conf file.

Unless stated otherwise
a default value is provided for each of the variables mentioned above
in `defaults/main.yml`.


## Dependencies

None.


## Playbooks

Example:

    - hosts: servers
      vars:
        debug: yes

        dhcp_server_interfaces: [ eth0 ]

        dhcp_server_keys:
          DHCP_UPDATER:
            algorithm: hmac-md5
            secret: pRP5FapFoJ95JEL06sv4PQ==

        dhcp_server_global:
          default-lease-time: 86400

        dhcp_server_classes:
          myhosts:
            config: |
              match hardware;
            subclasses: |
              1:10:bf:48:xx:xx:xx
              1:10:bf:48:xx:xx:xx

        dhcp_server_failover:
          foo: |
            primary
            address {{ ansible_eth0.ipv4.address }}
            port 519
            peer address {{ ansible_eth0.ipv4.address }}
            peer port 520
            max-response-delay 60
            max-unacked-updates 10
            mclt 3600
            split 128
            load balance max seconds 3

        dhcp_server_zones:
          example.org:
            key: DHCP_UPDATER
            primary: "{{ ansible_eth0.ipv4.address }}"

        dhcp_server_groups:
          group1:
            config: |
              filename "Xncd19r"
              next-server ncd-booter
            hosts:
              ncd1: |
                hardware ethernet 0:c0:c3:49:2b:57
              ncd4: |
                hardware ethernet 0:c0:c3:80:fc:32
              ncd8: |
                hardware ethernet 0:c0:c3:22:46:81

        dhcp_server_shared_networks:
          name:
            config: |
              option domain-name "test.redhat.com"
              option domain-name-servers ns1.redhat.com, ns2.redhat.com
              option routers 192.168.0.254
            subnets:
              192.168.1.0_255.255.255.0:
                config: |
                  range 192.168.1.1 192.168.1.254
              192.168.2.0_255.255.255.0:
                config: |
                  range 192.168.2.1 192.168.2.254

        dhcp_server_subnets:
          204.254.239.64_255.255.255.224:
            config: |
              range 204.254.239.74 204.254.239.94
              default-lease-time 86400
              max-lease-time 86400
            hosts:
              myhost: |
                fixed-address 192.168.0.249
                hardware ethernet 00:01:08:00:ad:33
          10.0.0.0_255.255.0.0:
            config: |
              option routers 10.0.254.254
            pools:
              ns: |
                option domain-name-servers ns1.example.com, ns2.example.com
                failover peer "foo"
                max-lease-time 28800
                range 10.0.0.5 10.0.0.199
                deny unknown-clients
              bogus: |
                option domain-name-servers bogus.example.com
                failover peer "foo"
                max-lease-time 300
                range 10.0.0.200 10.0.0.253
                allow unknown-clients

      roles:
         - role: saucelabs-ansible.dhcp_server
           tags: [ dhcp, server ]


## Tags

- **apparmor**: apparmor configuration tasks.
- **configuration**: configuration tasks.
- **debug**: role variables debug task.
- **installation**: installation tasks.
- **validation**: role variables validation task.


## Test

To run the tests you will need to install:

- [tox](https://tox.readthedocs.org/)
- [vagrant](https://www.vagrantup.com/)

To run all tests against all pre-defined OS/distributions * ansible versions:

```
$ tox
```

To run tests for `trusty64`:

```
$ cd tests
$ bash test_idempotence.sh --box trusty64.vagrant.dev
# log file will be stores under tests/log
```

To perform debugging on a specific environment:

```
$ cd tests
$ vagrant up trusty64.vagrant.dev

# to provision using the test.yml playbook (as many time as you need)
$ vagrant provision trusty64.vagrant.dev

# to access the Vagrant box
$ vagrant ssh trusty64.vagrant.dev
```


## Links

- [Ubuntu 14.04 > Ubuntu Server Guide > Networking > Dynamic Host Configuration Protocol (DHCP)](https://help.ubuntu.com/lts/serverguide/dhcp.html)
- [Ubuntu > dhcp3 server](https://help.ubuntu.com/community/dhcp3-server)


## License

BSD


## Author Information

- [esacteksab](https://github.com/esacteksab/)
- [steenzout](https://github.com/steenzout/)
