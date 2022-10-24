# Lab Report: Configuration Management With Ansible

## 2.1. Set up the lab environment
Het opstarten van de machine ging vlot, en de VM was ook niet veranderd, zoals de console output ook toont. 
```
PLAY RECAP *********************************************************************
srv010                     : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
Ook het inloggen verliep vlot, daar had ik geen probleem mee.
```
vmlab> vagrant ssh srv010

This system is built by the Bento project by Chef Software
More information can be found at https://github.com/chef/bento
Last login: Mon Oct 24 08:53:14 2022 from 10.0.2.2
[vagrant@srv010 ~]$
```

- ip adressen: 
```
[vagrant@srv010 ~]$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:bb:4f:a7 brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global dynamic noprefixroute eth0
       valid_lft 85712sec preferred_lft 85712sec
    inet6 fe80::df07:5913:66b5:4c63/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
3: eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:59:68:0a brd ff:ff:ff:ff:ff:ff
    inet 172.16.128.10/16 brd 172.16.255.255 scope global noprefixroute eth1
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe59:680a/64 scope link
       valid_lft forever preferred_lft forever
```

- Linux Distro: `AlmaLinux 8.4`
- Linux Kernel Version: `4.18.0-305.17.1.el8_4.x86_64`
##  2.2. Basic server setup
Ik probeerde deze stap te doen, en was zeer verbaasd toen het niet werkte. Ik had natuurlijk niet verder gelezen dat het effectief nog een error zou geven. Dan probeerde ik het `role-deps.sh`-script te runnen, maar dit gaf veel errors en het leek niet te werken.  


## 2.3. Web application server

### 2.3.1. MariaDB database server

### 2.3.2. Apache web server

### 2.3.3. WordPress

## 2.4. DNS

### Caching name server

### Authoritative name server

## 2.5. DHCP

## 2.6. Router

### Create and boot the router VM

### Check the default configuration

### Managing the router with Ansible

### Writing the playbook

## 2.7. Integration: a working LAN

## Reflection

## Resources

List all sources of useful information that you encountered while completing this assignment: books, manuals, HOWTO's, blog posts, etc.
