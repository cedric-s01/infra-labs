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

De nieuwe server opzetten met bind verliep vlekkeloos (voorlopig toch), bind was geïnstalleerd en ik kon beginnen aan de volgende configuratie, waar ik al vaak over gehoord had dat deze de lastigste was.

### Caching name server

Ik heb alle variabelen vlot kunnen instellen, wat met de duidelijke documentatie niet zeer moeilijk was.

- check the service state with `systemctl`
De service runt zoals verwacht
```
[vagrant@srv001 ~]$ sudo systemctl status named
● named.service - Berkeley Internet Name Domain (DNS)
    Loaded: loaded (/usr/lib/systemd/system/named.service; enabled; vendor preset: disabled)
    Active: active (running) since Tue 2022-12-06 10:03:36 UTC; 2s ago
    Process: 6671 ExecStop=/bin/sh -c /usr/sbin/rndc stop > /dev/null 2>&1 || /bin/kill -TERM $MAINPID (code=exited, status=0/SUCCESS)
    Process: 6688 ExecStart=/usr/sbin/named -u named -c ${NAMEDCONF} $OPTIONS (code=exited, status=0/SUCCESS)
    Process: 6684 ExecStartPre=/bin/bash -c if [ ! "$DISABLE_ZONE_CHECKING" == "yes" ]; then /usr/sbin/named-checkconf -z "$NAMEDCONF"; else echo "Checking of zone files is disabled"; fi (code=exited, status=0/SUCCESS)
    Main PID: 6689 (named)
    Tasks: 5 (limit: 4949)
    Memory: 13.9M
    CGroup: /system.slice/named.service
           └─6689 /usr/sbin/named -u named -c /etc/named.conf

    Dec 06 10:03:36 srv001 named[6689]: managed-keys-zone: loaded serial 5
    Dec 06 10:03:36 srv001 named[6689]: zone 0.in-addr.arpa/IN: loaded serial 0
    Dec 06 10:03:36 srv001 named[6689]: zone 1.0.0.127.in-addr.arpa/IN: loaded serial 0
    Dec 06 10:03:36 srv001 named[6689]: zone localhost.localdomain/IN: loaded serial 0
    Dec 06 10:03:36 srv001 named[6689]: zone 1.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.ip6.arpa/IN: loaded serial 0
    Dec 06 10:03:36 srv001 named[6689]: zone localhost/IN: loaded serial 0
    Dec 06 10:03:36 srv001 named[6689]: all zones loaded
    Dec 06 10:03:36 srv001 named[6689]: running
    Dec 06 10:03:36 srv001 systemd[1]: Started Berkeley Internet Name Domain (DNS).
    Dec 06 10:03:36 srv001 named[6689]: managed-keys-zone: No DNSKEY RRSIGs found for '.': success
```
- what sockets/ports are in use? (`ss`)

```
[vagrant@srv001 ~]$ ss -tulnp
Netid     State       Recv-Q      Send-Q           Local Address:Port           Peer Address:Port     Process
udp       UNCONN      0           0                    127.0.0.1:53                  0.0.0.0:*
udp       UNCONN      0           0                127.0.0.53%lo:53                  0.0.0.0:*
udp       UNCONN      0           0                      0.0.0.0:111                 0.0.0.0:*
udp       UNCONN      0           0                      0.0.0.0:5355                0.0.0.0:*
udp       UNCONN      0           0                    127.0.0.1:323                 0.0.0.0:*
udp       UNCONN      0           0                        [::1]:53                     [::]:*
udp       UNCONN      0           0                         [::]:111                    [::]:*
udp       UNCONN      0           0                         [::]:5355                   [::]:*
udp       UNCONN      0           0                        [::1]:323                    [::]:*
tcp       LISTEN      0           128                    0.0.0.0:5355                0.0.0.0:*
tcp       LISTEN      0           128                    0.0.0.0:111                 0.0.0.0:*
tcp       LISTEN      0           10                   127.0.0.1:53                  0.0.0.0:*
tcp       LISTEN      0           128                    0.0.0.0:22                  0.0.0.0:*
tcp       LISTEN      0           128                  127.0.0.1:953                 0.0.0.0:*
tcp       LISTEN      0           128                       [::]:5355                   [::]:*
tcp       LISTEN      0           128                       [::]:111                    [::]:*
tcp       LISTEN      0           10                       [::1]:53                     [::]:*
tcp       LISTEN      0           128                       [::]:22                     [::]:*
tcp       LISTEN      0           128                      [::1]:953                    [::]:*
```
- check the service logs with `journalctl`

```
-- Reboot --
Dec 06 09:43:48 srv001 systemd[1]: Starting Berkeley Internet Name Domain (DNS)...
Dec 06 09:43:48 srv001 bash[776]: zone localhost.localdomain/IN: loaded serial 0
Dec 06 09:43:48 srv001 bash[776]: zone localhost/IN: loaded serial 0
Dec 06 09:43:48 srv001 bash[776]: zone 1.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.ip6.arpa/IN: loaded serial 0
Dec 06 09:43:48 srv001 bash[776]: zone 1.0.0.127.in-addr.arpa/IN: loaded serial 0
Dec 06 09:43:48 srv001 bash[776]: zone 0.in-addr.arpa/IN: loaded serial 0
Dec 06 09:43:48 srv001 named[800]: starting BIND 9.11.36-RedHat-9.11.36-5.el8_7.2 (Extended Support Version) <id:68dbd5b>
Dec 06 09:43:48 srv001 named[800]: running on Linux x86_64 4.18.0-305.17.1.el8_4.x86_64 #1 SMP Wed Sep 8 03:45:51 EDT 2021
Dec 06 09:43:48 srv001 named[800]: built with '--build=x86_64-redhat-linux-gnu' '--host=x86_64-redhat-linux-gnu' '--program-prefix=' '--disable-dependency-tracking' '--prefix=/usr' '--exec-prefix=/usr' '--bindir=/usr/bin' '--sbindir=/usr/sbin' '--sysconfdir=/etc' '--datadir=/usr/share' '--includedir=/usr/include' '--libdir=/usr/lib64' '--libexecdir=/usr/libexec' '--sharedstatedir=/var/lib' '--mandir=/usr/share/man' '--infodir=/usr/share/info' '--with-python=/usr/libexec/platform-python' '--with-libtool' '--localstatedir=/var' '--enable-threads' '--enable-ipv6' '--enable-filter-aaaa' '--with-pic' '--disable-static' '--includedir=/usr/include/bind9' '--with-tuning=large' '--with-libidn2' '--enable-openssl-hash' '--with-geoip2' '--enable-native-pkcs11' '--with-pkcs11=/usr/lib64/pkcs11/libsofthsm2.so' '--with-dlopen=yes' '--with-dlz-ldap=yes' '--with-dlz-postgres=yes' '--with-dlz-mysql=yes' '--with-dlz-files>
Dec 06 09:43:48 srv001 named[800]: running as: named -u named -c /etc/named.conf
Dec 06 09:43:48 srv001 named[800]: compiled by GCC 8.5.0 20210514 (Red Hat 8.5.0-15)
Dec 06 09:43:48 srv001 named[800]: compiled with OpenSSL version: OpenSSL 1.1.1k  FIPS 25 Mar 2021
Dec 06 09:43:48 srv001 named[800]: linked to OpenSSL version: OpenSSL 1.1.1g FIPS  21 Apr 2020
Dec 06 09:43:48 srv001 named[800]: compiled with libxml2 version: 2.9.7
Dec 06 09:43:48 srv001 named[800]: linked to libxml2 version: 20907
Dec 06 09:43:48 srv001 named[800]: compiled with libjson-c version: 0.13.1
Dec 06 09:43:48 srv001 named[800]: linked to libjson-c version: 0.13.1
Dec 06 09:43:48 srv001 named[800]: compiled with zlib version: 1.2.11
Dec 06 09:43:48 srv001 named[800]: linked to zlib version: 1.2.11
Dec 06 09:43:48 srv001 named[800]: threads support is enabled
Dec 06 09:43:48 srv001 named[800]: ----------------------------------------------------
Dec 06 09:43:48 srv001 named[800]: BIND 9 is maintained by Internet Systems Consortium,
Dec 06 09:43:48 srv001 named[800]: Inc. (ISC), a non-profit 501(c)(3) public-benefit
Dec 06 09:43:48 srv001 named[800]: corporation.  Support and training for BIND 9 are
Dec 06 09:43:48 srv001 named[800]: available at https://www.isc.org/support
Dec 06 09:43:48 srv001 named[800]: ----------------------------------------------------
Dec 06 09:43:48 srv001 named[800]: adjusted limit on open files from 262144 to 1048576
Dec 06 09:43:48 srv001 named[800]: found 2 CPUs, using 2 worker threads
Dec 06 09:43:48 srv001 named[800]: using 1 UDP listener per interface
Dec 06 09:43:48 srv001 named[800]: using up to 21000 sockets
Dec 06 09:43:48 srv001 named[800]: loading configuration from '/etc/named.conf'
Dec 06 09:43:48 srv001 named[800]: unable to open '/etc/named.iscdlv.key'; using built-in keys instead
Dec 06 09:43:48 srv001 named[800]: looking for GeoIP2 databases in '/usr/share/GeoIP'
Dec 06 09:43:48 srv001 named[800]: opened GeoIP2 database '/usr/share/GeoIP/GeoLite2-Country.mmdb'
Dec 06 09:43:48 srv001 named[800]: opened GeoIP2 database '/usr/share/GeoIP/GeoLite2-City.mmdb'
Dec 06 09:43:48 srv001 named[800]: using default UDP/IPv4 port range: [32768, 60999]
Dec 06 09:43:48 srv001 named[800]: using default UDP/IPv6 port range: [32768, 60999]
Dec 06 09:43:48 srv001 named[800]: listening on IPv4 interface lo, 127.0.0.1#53
Dec 06 09:43:48 srv001 named[800]: listening on IPv6 interface lo, ::1#53
Dec 06 09:43:48 srv001 named[800]: generating session key for dynamic DNS
Dec 06 09:43:48 srv001 named[800]: sizing zone task pool based on 5 zones
Dec 06 09:43:48 srv001 named[800]: none:105: 'max-cache-size 90%' - setting to 728MB (out of 809MB)
Dec 06 09:43:48 srv001 named[800]: set up managed keys zone for view _default, file '/var/named/dynamic/managed-keys.bind'
Dec 06 09:43:48 srv001 named[800]: none:105: 'max-cache-size 90%' - setting to 728MB (out of 809MB)
Dec 06 09:43:48 srv001 named[800]: configuring command channel from '/etc/rndc.key'
Dec 06 09:43:48 srv001 named[800]: command channel listening on 127.0.0.1#953
Dec 06 09:43:48 srv001 named[800]: configuring command channel from '/etc/rndc.key'
Dec 06 09:43:48 srv001 named[800]: command channel listening on ::1#953
Dec 06 09:43:48 srv001 named[800]: managed-keys-zone: journal file is out of date: removing journal file
Dec 06 09:43:48 srv001 named[800]: managed-keys-zone: loaded serial 4
Dec 06 09:43:48 srv001 named[800]: zone 1.0.0.127.in-addr.arpa/IN: loaded serial 0
Dec 06 09:43:48 srv001 named[800]: zone 0.in-addr.arpa/IN: loaded serial 0
Dec 06 09:43:48 srv001 named[800]: zone localhost/IN: loaded serial 0
Dec 06 09:43:48 srv001 named[800]: zone 1.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.ip6.arpa/IN: loaded serial 0
Dec 06 09:43:48 srv001 named[800]: zone localhost.localdomain/IN: loaded serial 0
Dec 06 09:43:48 srv001 named[800]: all zones loaded
Dec 06 09:43:48 srv001 systemd[1]: Started Berkeley Internet Name Domain (DNS).
Dec 06 09:43:48 srv001 named[800]: running
Dec 06 09:43:48 srv001 named[800]: network unreachable resolving './DNSKEY/IN': 8.8.8.8#53
Dec 06 09:43:48 srv001 named[800]: managed-keys-zone: Unable to fetch DNSKEY set '.': SERVFAIL
Dec 06 10:03:36 srv001 systemd[1]: Stopping Berkeley Internet Name Domain (DNS)...
Dec 06 10:03:36 srv001 named[800]: received control channel command 'stop'
Dec 06 10:03:36 srv001 named[800]: shutting down: flushing changes
Dec 06 10:03:36 srv001 named[800]: stopping command channel on 127.0.0.1#953
Dec 06 10:03:36 srv001 named[800]: stopping command channel on ::1#953
Dec 06 10:03:36 srv001 named[800]: no longer listening on 127.0.0.1#53
Dec 06 10:03:36 srv001 named[800]: no longer listening on ::1#53
Dec 06 10:03:36 srv001 named[800]: exiting
Dec 06 10:03:36 srv001 systemd[1]: named.service: Succeeded.
Dec 06 10:03:36 srv001 systemd[1]: Stopped Berkeley Internet Name Domain (DNS).
Dec 06 10:03:36 srv001 systemd[1]: Starting Berkeley Internet Name Domain (DNS)...
Dec 06 10:03:36 srv001 bash[6684]: zone localhost.localdomain/IN: loaded serial 0
Dec 06 10:03:36 srv001 bash[6684]: zone localhost/IN: loaded serial 0
Dec 06 10:03:36 srv001 bash[6684]: zone 1.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.ip6.arpa/IN: loaded serial 0
Dec 06 10:03:36 srv001 bash[6684]: zone 1.0.0.127.in-addr.arpa/IN: loaded serial 0
Dec 06 10:03:36 srv001 bash[6684]: zone 0.in-addr.arpa/IN: loaded serial 0
Dec 06 10:03:36 srv001 named[6689]: starting BIND 9.11.36-RedHat-9.11.36-5.el8_7.2 (Extended Support Version) <id:68dbd5b>
Dec 06 10:03:36 srv001 named[6689]: running on Linux x86_64 4.18.0-305.17.1.el8_4.x86_64 #1 SMP Wed Sep 8 03:45:51 EDT 2021
Dec 06 10:03:36 srv001 named[6689]: built with '--build=x86_64-redhat-linux-gnu' '--host=x86_64-redhat-linux-gnu' '--program-prefix=' '--disable-dependency-tracking' '--prefix=/usr' '--exec-prefix=/usr' '--bindir=/usr/bin' '--sbindir=/usr/sbin' '--sysconfdir=/etc' '--datadir=/usr/share' '--includedir=/usr/include' '--libdir=/usr/lib64' '--libexecdir=/usr/libexec' '--sharedstatedir=/var/lib' '--mandir=/usr/share/man' '--infodir=/usr/share/info' '--with-python=/usr/libexec/platform-python' '--with-libtool' '--localstatedir=/var' '--enable-threads' '--enable-ipv6' '--enable-filter-aaaa' '--with-pic' '--disable-static' '--includedir=/usr/include/bind9' '--with-tuning=large' '--with-libidn2' '--enable-openssl-hash' '--with-geoip2' '--enable-native-pkcs11' '--with-pkcs11=/usr/lib64/pkcs11/libsofthsm2.so' '--with-dlopen=yes' '--with-dlz-ldap=yes' '--with-dlz-postgres=yes' '--with-dlz-mysql=yes' '--with-dlz-file>
Dec 06 10:03:36 srv001 named[6689]: running as: named -u named -c /etc/named.conf
Dec 06 10:03:36 srv001 named[6689]: compiled by GCC 8.5.0 20210514 (Red Hat 8.5.0-15)
Dec 06 10:03:36 srv001 named[6689]: compiled with OpenSSL version: OpenSSL 1.1.1k  FIPS 25 Mar 2021
Dec 06 10:03:36 srv001 named[6689]: linked to OpenSSL version: OpenSSL 1.1.1g FIPS  21 Apr 2020
Dec 06 10:03:36 srv001 named[6689]: compiled with libxml2 version: 2.9.7
Dec 06 10:03:36 srv001 named[6689]: linked to libxml2 version: 20907
Dec 06 10:03:36 srv001 named[6689]: compiled with libjson-c version: 0.13.1
Dec 06 10:03:36 srv001 named[6689]: linked to libjson-c version: 0.13.1
Dec 06 10:03:36 srv001 named[6689]: compiled with zlib version: 1.2.11
Dec 06 10:03:36 srv001 named[6689]: linked to zlib version: 1.2.11
Dec 06 10:03:36 srv001 named[6689]: threads support is enabled
Dec 06 10:03:36 srv001 named[6689]: ----------------------------------------------------
Dec 06 10:03:36 srv001 named[6689]: BIND 9 is maintained by Internet Systems Consortium,
Dec 06 10:03:36 srv001 named[6689]: Inc. (ISC), a non-profit 501(c)(3) public-benefit
Dec 06 10:03:36 srv001 named[6689]: corporation.  Support and training for BIND 9 are
Dec 06 10:03:36 srv001 named[6689]: available at https://www.isc.org/support
Dec 06 10:03:36 srv001 named[6689]: ----------------------------------------------------
Dec 06 10:03:36 srv001 named[6689]: adjusted limit on open files from 262144 to 1048576
Dec 06 10:03:36 srv001 named[6689]: found 2 CPUs, using 2 worker threads
Dec 06 10:03:36 srv001 named[6689]: using 1 UDP listener per interface
Dec 06 10:03:36 srv001 named[6689]: using up to 21000 sockets
Dec 06 10:03:36 srv001 named[6689]: loading configuration from '/etc/named.conf'
Dec 06 10:03:36 srv001 named[6689]: unable to open '/etc/named.iscdlv.key'; using built-in keys instead
Dec 06 10:03:36 srv001 named[6689]: looking for GeoIP2 databases in '/usr/share/GeoIP'
Dec 06 10:03:36 srv001 named[6689]: opened GeoIP2 database '/usr/share/GeoIP/GeoLite2-Country.mmdb'
Dec 06 10:03:36 srv001 named[6689]: opened GeoIP2 database '/usr/share/GeoIP/GeoLite2-City.mmdb'
Dec 06 10:03:36 srv001 named[6689]: using default UDP/IPv4 port range: [32768, 60999]
Dec 06 10:03:36 srv001 named[6689]: using default UDP/IPv6 port range: [32768, 60999]
Dec 06 10:03:36 srv001 named[6689]: listening on IPv4 interface lo, 127.0.0.1#53
Dec 06 10:03:36 srv001 named[6689]: listening on IPv6 interface lo, ::1#53
Dec 06 10:03:36 srv001 named[6689]: generating session key for dynamic DNS
Dec 06 10:03:36 srv001 named[6689]: sizing zone task pool based on 5 zones
Dec 06 10:03:36 srv001 named[6689]: none:105: 'max-cache-size 90%' - setting to 728MB (out of 809MB)
Dec 06 10:03:36 srv001 named[6689]: set up managed keys zone for view _default, file '/var/named/dynamic/managed-keys.bind'
Dec 06 10:03:36 srv001 named[6689]: none:105: 'max-cache-size 90%' - setting to 728MB (out of 809MB)
Dec 06 10:03:36 srv001 named[6689]: configuring command channel from '/etc/rndc.key'
Dec 06 10:03:36 srv001 named[6689]: command channel listening on 127.0.0.1#953
Dec 06 10:03:36 srv001 named[6689]: configuring command channel from '/etc/rndc.key'
Dec 06 10:03:36 srv001 named[6689]: command channel listening on ::1#953
Dec 06 10:03:36 srv001 named[6689]: managed-keys-zone: journal file is out of date: removing journal file
Dec 06 10:03:36 srv001 named[6689]: managed-keys-zone: loaded serial 5
Dec 06 10:03:36 srv001 named[6689]: zone 0.in-addr.arpa/IN: loaded serial 0
Dec 06 10:03:36 srv001 named[6689]: zone 1.0.0.127.in-addr.arpa/IN: loaded serial 0
Dec 06 10:03:36 srv001 named[6689]: zone localhost.localdomain/IN: loaded serial 0
Dec 06 10:03:36 srv001 named[6689]: zone 1.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.ip6.arpa/IN: loaded serial 0
Dec 06 10:03:36 srv001 named[6689]: zone localhost/IN: loaded serial 0
Dec 06 10:03:36 srv001 named[6689]: all zones loaded
Dec 06 10:03:36 srv001 named[6689]: running
Dec 06 10:03:36 srv001 systemd[1]: Started Berkeley Internet Name Domain (DNS).
Dec 06 10:03:36 srv001 named[6689]: managed-keys-zone: No DNSKEY RRSIGs found for '.': success
```
- look at the contents of the main configuration file

```
[vagrant@srv001 ~]$ sudo cat /etc/named.conf
//
// named.conf
//
//
// Ansible managed
//
//
options {
  listen-on port 53 { 127.0.0.1; };
  listen-on-v6 port 53 { ::1; };
  directory   "/var/named";
  dump-file   "/var/named/data/cache_dump.db";
  statistics-file "/var/named/data/named_stats.txt";
  memstatistics-file "/var/named/data/named_mem_stats.txt";
  allow-query     { any; };

  recursion yes;
  allow-recursion { any; };
  forwarders { 8.8.8.8; };  forward only;
  rrset-order { order random; };

  dnssec-validation True;

  /* Path to ISC DLV key */
  bindkeys-file "/etc/named.iscdlv.key";

  managed-keys-directory "/var/named/dynamic";

  pid-file "/run/named/named.pid";
  session-keyfile "/run/named/session.key";
};


logging {
  channel default_debug {
    file "data/named.run";
    severity dynamic;
    print-time yes;
  };
};

include "/etc/named.root.key";
include "/etc/named.rfc1912.zones";
```
- send a query to the DNS service with `dig` and check if it responds

```
[vagrant@srv001 ~]$ dig google.com

; <<>> DiG 9.11.36-RedHat-9.11.36-5.el8_7.2 <<>> google.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 10991
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 4, ADDITIONAL: 9

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
; COOKIE: 484ae45016c066c509c6e904638f1a824a4c05aca3dd9f42 (good)
;; QUESTION SECTION:
;google.com.                    IN      A

;; ANSWER SECTION:
google.com.             186     IN      A       142.251.36.14

;; AUTHORITY SECTION:
google.com.             36909   IN      NS      ns3.google.com.
google.com.             36909   IN      NS      ns4.google.com.
google.com.             36909   IN      NS      ns2.google.com.
google.com.             36909   IN      NS      ns1.google.com.

;; ADDITIONAL SECTION:
ns4.google.com.         85292   IN      A       216.239.38.10
ns3.google.com.         333117  IN      A       216.239.36.10
ns1.google.com.         80550   IN      A       216.239.32.10
ns2.google.com.         7886    IN      A       216.239.34.10
ns4.google.com.         87386   IN      AAAA    2001:4860:4802:38::a
ns3.google.com.         333117  IN      AAAA    2001:4860:4802:36::a
ns1.google.com.         342345  IN      AAAA    2001:4860:4802:32::a
ns2.google.com.         7886    IN      AAAA    2001:4860:4802:34::a

;; Query time: 2 msec
;; SERVER: 10.0.2.3#53(10.0.2.3)
;; WHEN: Tue Dec 06 10:33:38 UTC 2022
;; MSG SIZE  rcvd: 331
```
- send a query from your physical system and check if it responds

```

```
- check the logs again, can you see which queries were sent a what response the server gave?


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
