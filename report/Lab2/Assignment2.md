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

## 2.2. Basic server setup

Ik probeerde deze stap te doen, en was zeer verbaasd toen het niet werkte. Ik had natuurlijk niet verder gelezen dat het effectief nog een error zou geven. Dan probeerde ik het `role-deps.sh`-script te runnen via Powershell, zowel als user en administrator, maar dit gaf veel errors en het leek niet te werken. Na dit te vragen heb ik het geprobeerd in een Git-Bash terminal, en dit werkte wel.
Maar na `vagrant provision` te runnen, kreeg ik de error dat er geen module genaamd SELinux aanwezig was, en dat er dus geen Python library genaamd `libselinux-python` kon geimporteerd worden (zie ook volgende error)

```
TASK [bertvv.rh-base : Security | Make sure SELinux has the desired state (enforcing)] ***
An exception occurred during task execution. To see the full traceback, use -vvv. The error was: ModuleNotFoundError: No module named 'selinux'
fatal: [srv010]: FAILED! => {"changed": false, "msg": "Failed to import the required Python library (libselinux-python) on srv010's Python /usr/bin/python3.8. Please read the module documentation and install it in the appropriate location. If the required library is installed, but Ansible is using the wrong Python interpreter, please consult the documentation on ansible_python_interpreter"}
```

Door wat opzoekwerk te doen in de discord-server heb ik een oplossing hiervoor gevonden, aangezien een aantal leerlingen hetzelfde probleem hadden vorig jaar.

De SSH-connectie verliep probleemloos, ik kon direct verbinding maken met de VM

```
$ ssh cedric@172.16.128.10
The authenticity of host '172.16.128.10 (172.16.128.10)' can't be established.
ED25519 key fingerprint is SHA256:OfN1dlQqG1ceLJSwasMIGt/mwWZHWCgbZ0UhgyBDU3c.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '172.16.128.10' (ED25519) to the list of known hosts.
cedric@172.16.128.10's password:

This system is built by the Bento project by Chef Software
More information can be found at https://github.com/chef/bento
[cedric@srv010 ~]$

```

## 2.3. Web application server

### 2.3.1. MariaDB database server

Voor de installatie van de mariadb-service wist ik welke variabelen ik moest gebruiken, behalve voor het randomized password dus dit heb ik moeten opzoeken. Maar toen ik `vagrant provision` runde, kreeg ik volgende error

```
TASK [bertvv.mariadb : Install packages] ***************************************
fatal: [srv010]: FAILED! => {"changed": false, "msg": "Failed to download metadata for repo 'MariaDB': Cannot download repomd.xml: Cannot download repodata/repomd.xml: All mirrors were tried", "rc": 1, "results": []}

PLAY RECAP *********************************************************************
srv010                     : ok=36   changed=3    unreachable=0    failed=1    skipped=23   rescued=0    ignored=0

Ansible failed to complete successfully. Any error output should be
visible above. Please fix these errors and try again.
```

Ik heb eens gekeken in de Teams chat, waar er stond dat ik een andere sql-rol moest gebruiken, wat ik dan ook gedaan heb. Het duurde even om dit alles op te lossen, en uiteindelijk kreeg ik een nieuwe error, namelijk 
```
$ vagrant up
Bringing machine 'srv010' up with 'virtualbox' provider...
==> srv010: Checking if box 'bento/almalinux-8' version '202109.10.0' is up to date...
==> srv010: A newer version of the box 'bento/almalinux-8' for provider 'virtualbox' is
==> srv010: available! You currently have version '202109.10.0'. The latest is version
==> srv010: '202206.14.0'. Run `vagrant box update` to update.
==> srv010: Clearing any previously set forwarded ports...
==> srv010: Clearing any previously set network interfaces...
==> srv010: Preparing network interfaces based on configuration...
    srv010: Adapter 1: nat
    srv010: Adapter 2: hostonly
==> srv010: Forwarding ports...
    srv010: 22 (guest) => 2222 (host) (adapter 1)
==> srv010: Running 'pre-boot' VM customizations...
==> srv010: Booting VM...
==> srv010: Waiting for machine to boot. This may take a few minutes...
    srv010: SSH address: 127.0.0.1:2222
    srv010: SSH username: vagrant
    srv010: SSH auth method: private key
==> srv010: Machine booted and ready!
==> srv010: Checking for guest additions in VM...
    srv010: No guest additions were detected on the base box for this VM! Guest
    srv010: additions are required for forwarded ports, shared folders, host only
    srv010: networking, and more. If SSH fails on this machine, please install
    srv010: the guest additions and repackage the box to continue.
    srv010:
    srv010: This is not an error message; everything may continue to work properly,
    srv010: in which case you may ignore this message.
==> srv010: Setting hostname...
==> srv010: Configuring and enabling network interfaces...
==> srv010: Mounting shared folders...
    srv010: /vagrant => C:/Users/cspel/Documents/School/Jaar 3/Sem 1/Infra Auto/infra-2223-cedric-s01/vmlab
Vagrant was unable to mount VirtualBox shared folders. This is usually
because the filesystem "vboxsf" is not available. This filesystem is
made available via the VirtualBox Guest Additions and kernel module.
Please verify that these guest additions are properly installed in the
guest. This is not a bug in Vagrant and is usually caused by a faulty
Vagrant box. For context, the command attempted was:

mount -t vboxsf -o uid=1000,gid=1000,_netdev vagrant /vagrant

The error output from the command was:

/sbin/mount.vboxsf: mounting failed with the error: No such device
```
Dit was compleet onlogisch in mijn ogen, omdat de VM gisteren wel perfect in orde was, en ik niks had aangepast. Na wat opzoekwerk was dit ook opgelost, dus kon ik eindelijk verder werken aan de opstelling. 

### 2.3.2. Apache web server

De Apache webserver was vrij snel opgezet, en het certificaat was gemaakt. Maar toen ik het de config files aanpaste, kreeg ik als reactie een crash van de httpd-service. Dit was omdat de Fully Qualified Domain Name niet overeenkwam met de ServerName, dus dit heb ik aangepast in de .yml files. Nadien werkte alles zoals het hoorde, en heb ik httpd toegevoegd aan de firewall-services. Hierna kon ik de server bereiken vanaf mijn host-systeem, en kreeg ik de AlmaLinux Test Page

### 2.3.3. WordPress

Wordpress installeren ging ook vlot, tot ik `https://172.16.128.10/wordpress/` wou bereiken. Hierbij kreeg ik `Error establishing a database connection` als error. Na wat opzoekwerk had ik door dat de credentials in de wp-config file niet overeenkwamen met de database-credentials. Dit heb ik aangepast, en dan kreeg ik `There has been a critical error on this website`. Dit bleek een PHP-error te zijn, dus dan heb ik geprobeerd om de PHP-versie te updaten. Na wat research heb ik in de TI-discord opnieuw mijn toevlucht gezocht, waar men zei dat de nieuwste versie van de wordpress-role niet werkte en je een versie moest terugschakelen. Na dit ook te proberen, zonder resultaat, heb ik in de Teams-chat de suggestie gevonden om de php-json package te installeren, en dit heeft dan eindelijk gewerkt. 

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
