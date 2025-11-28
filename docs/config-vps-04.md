Configuration initiale du VPS (4)
=================================

  * Désactiver l'IPv6

L'IPv6 est le protocole réseau du futur et nous l'utiliserons donc dans le
futur. :o)

Plus sérieusement, nous pouvons sereinement le désactiver ici dans la mesure où
il ne nous sert à rien.

Éditer un fichier `/etc/sysctl.d/ipv6.conf` comme ceci :

```conf
# /etc/sysctl.d/ipv6.conf
#
# Disable IPv6
net.ipv6.conf.all.disable_ipv6 = 1
net.ipv6.conf.default.disable_ipv6 = 1
```

Prendre en compte ces nouveaux paramètres :

```console
$ sudo sysctl -p --load /etc/sysctl.d/ipv6.conf
net.ipv6.conf.all.disable_ipv6 = 1
net.ipv6.conf.default.disable_ipv6 = 1
```

À partir de là, `ip addr` n'affiche plus l'adresse IPv6 de la machine.

