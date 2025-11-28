Configuration initiale du VPS (2)
=================================

  * Mise à jour initiale

  * État des lieux de l'installation

  * Dépôts de paquets officiels et tiers

  * Ajout du dépôt EPEL


Connexion au VPS :

```console
$ ssh microlinux@51.178.223.37
```

Mise à jour initiale :

```console
$ sudo dnf update
```

Si le noyau a été mis à jour :

```console
$ sudo reboot && exit
```

> Rappel sur les mises à jour et le cycle de support de Rocky Linux 8.

Dépôts de paquets configurés :

```console
$ dnf repolist
repo id                 repo name
appstream               Rocky Linux 8 - AppStream
baseos                  Rocky Linux 8 - BaseOS
extras                  Rocky Linux 8 - Extras
```

État des lieux sur ce qui est installé :

```console
$ rpm -qa | wc -l
467
$ dnf group list hidden | head -n 15
Available Environment Groups:
   Server with GUI
   Server
   Minimal Install
   Workstation
   Virtualization Host
   Custom Operating System
Installed Groups:
   KVM platform specific packages
   Core
Available Groups:
   Backup Client
   base-x
   Conflicts AppStream
```

Installer une panoplie d'outils :

```console
$ sudo dnf group install Base
```

Paquets disponibles dans la configuration par défaut :

```console
$ dnf list all | wc -l
7588
```

Certains paquets ne sont pas disponibles dans les dépôts de paquets officiels :

```console
$ dnf search certbot
No matches found.
$ dnf search cowsay
No matches found.
```

Activer le dépôt EPEL (*Extra Packages for Enterprise Linux*) :

```console
$ sudo dnf install epel-release
...
Many EPEL packages require the CodeReady Builder (CRB) repository.
It is recommended that you run /usr/bin/crb enable to enable the CRB repository.
```

Suivre la recommandation et activer le dépôt CRB :

```console
$ sudo /usr/bin/crb enable
Enabling CRB repo
CRB repo is enabled and named: powertools
```

Afficher la configuration des dépôts de paquets :

```console
$ dnf repolist
repo id             repo name
appstream           Rocky Linux 8 - AppStream
baseos              Rocky Linux 8 - BaseOS
epel                Extra Packages for Enterprise Linux 8 - x86_64
extras              Rocky Linux 8 - Extras
powertools          Rocky Linux 8 - PowerTools
```

On a beaucoup plus de choix maintenant :

```console
$ dnf list all | wc -l
20163
$ dnf search certbot
certbot.noarch : A free, automated certificate authority client
$ dnf search cowsay
cowsay.noarch : Configurable speaking/thinking cow
```

Afficher les infos sur un paquet donné :

```console
$ dnf info cowsay
Available Packages
Name         : cowsay
Version      : 3.7.0
Release      : 10.el8
Architecture : noarch
Size         : 47 k
Source       : cowsay-3.7.0-10.el8.src.rpm
Repository   : epel
Summary      : Configurable speaking/thinking cow
URL          : https://github.com/cowsay-org/cowsay
License      : GPL-2.0-or-later
Description  : cowsay is a configurable talking cow, written in Perl.  It operates
             : much as the figlet program does, and it is written in the same spirit
             : of silliness.
             : It generates ASCII pictures of a cow with a message. It can also generate
             : pictures of other animals.
```

> Dans les infos, notez l'indication du dépôt (`Repository`).

```console
$ sudo dnf install -y cowsay
$ cowsay Voili voilou !
 ________________
< Voili voilou ! >
 ----------------
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||
```

