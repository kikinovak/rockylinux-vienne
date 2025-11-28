Pare-feu simple avec FirewallD
==============================

Dans la configuration par défaut, le VPS ne dispose pas de pare-feu&nbsp;:

```console
$ rpm -q firewalld
package firewalld is not installed
```

On va donc lui en installer un&nbsp;:

```console
$ sudo dnf install -y firewalld
```

J'active et je lance le service&nbsp;:

```console
$ sudo systemctl enable firewalld --now
```

Je vérifie s'il tourne correctement&nbsp;:

```console
$ systemctl status firewalld
● firewalld.service - firewalld - dynamic firewall daemon
   Loaded: loaded (/usr/lib/systemd/system/firewalld.service; enabled; 
           vendor preset: enabled)
   Active: active (running) since Fri 2025-11-28 10:03:21 UTC; 14s ago
     Docs: man:firewalld(1)
 Main PID: 4388 (firewalld)
    Tasks: 2 (limit: 22065)
   Memory: 34.4M
   CGroup: /system.slice/firewalld.service
           └─4388 /usr/libexec/platform-python -s /usr/sbin/firewalld...
```

Par défaut la zone `public` est associée à l'interface `eth0`&nbsp;:

```console
$ nmcli con show
NAME         UUID                                  TYPE      DEVICE
System eth0  5fb06bd0-0bb0-7ffb-45f1-d6edd65f3e03  ethernet  eth0
ens3         61c74dca-d53f-4784-bbe4-9c898cdb0563  ethernet  --
$ firewall-cmd --get-active-zones
public
  interfaces: eth0
```

J'affiche la configuration de la zone&nbsp;:

```console
$ sudo firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: eth0
  sources:
  services: cockpit dhcpv6-client ssh
  ports:
  protocols:
  forward: no
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
```

Je supprime `cockpit` et `dhcpv6-client` pour ne garder que le seul service
ssh&nbsp;:

```console
$ sudo firewall-cmd --permanent --remove-service cockpit
success
$ sudo firewall-cmd --permanent --remove-service dhcpv6-client
success
$ sudo firewall-cmd --reload
success
$ sudo firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: eth0
  sources:
  services: ssh
  ports:
  protocols:
  forward: no
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
```


Pour en savoir plus
-------------------

  * [Configuration de base du pare-feu FirewallD sur un serveur Rocky Linux 8][1]

[1]: https://blog.microlinux.fr/firewalld-rocky-linux-8/
