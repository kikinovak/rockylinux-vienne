Configuration initiale du VPS
=============================

Connexion au VPS :

```console
$ ssh rocky@51.178.223.37
```

Afficher les infos sur le système :

```console
$ cat /etc/redhat-release
Rocky Linux release 8.10 (Green Obsidian)
$ cat /etc/os-release
NAME="Rocky Linux"
VERSION="8.10 (Green Obsidian)"
...
```


Utilisateur initial
-------------------

On vérifie ce que l'utilisateur `rocky` a le droit de faire :

```console
$ head -n 1 /etc/shadow
head: cannot open '/etc/shadow' for reading: Permission denied
$ sudo head -n 1 /etc/shadow
root:!!:19871:0:99999:7:::
```

On vérifie avec `visudo` :

```console
$ sudo visudo
...
rocky   ALL=(ALL)       NOPASSWD: ALL
```


Créer un utilisateur
--------------------

On va créer notre propre utilisateur initial.

```console
$ sudo useradd -c "Microlinux" microlinux
$ sudo passwd microlinux
Changing password for user microlinux.
New password: ********
Retype new password: ********
passwd: all authentication tokens updated successfully.
```

Dans la configuration par défaut, les membres du groupe `wheel` ont le droit
d'utiliser la commande `sudo` avec un mot de passe :

```console
$ sudo visudo
...
## Allows people in group wheel to run all commands
%wheel  ALL=(ALL)       ALL
```

On va donc ajouter l'utilisateur initial à ce groupe :

```console
$ sudo usermod -aG wheel microlinux
```

Dans la configuration actuelle du serveur SSH, cet utilisateur ne peut pas se
connecter. On va donc reconfigurer le serveur SSH. Et avant de faire ça, on va
installer un éditeur de texte.

```console
$ sudo dnf install -y vim-enhanced
```

Éditer /etc/ssh/sshd_config :

```console
# To disable tunneled clear text passwords, change to no here!
PasswordAuthentication yes
#PermitEmptyPasswords no
#PasswordAuthentication no
```

Prendre en compte la configuration :

```console
$ sudo systemctl reload sshd
```

À présent on peut se reconnecter avec le compte que l'on vient de créer :

```console
$ exit
logout
Connection to 51.178.223.37 closed.
$ ssh microlinux@51.178.223.37
microlinux@51.178.223.37's password: ************
```

Mettre en place l'authentification par clé SSH pour ce compte :

```console
$ exit
logout
Connection to 51.178.223.37 closed.
$ ssh-copy-id -i /home/kikinovak/.ssh/id_rsa.pub microlinux@51.178.223.37
```

Vérifier si la connexion par clé SSH fonctionne avec ce compte :

```console
$ ssh microlinux@51.178.223.37
```

> J'ai essayé de configurer la connexion par clé SSH pour le compte `root` mais
> ça bloque en amont au niveau de OVH.


Peaufinage de `sudo`
--------------------

Si l'on ne veut pas avoir à retaper le mot de passe pour `sudo` toutes les
cinq minutes, on peut créer un fichier `/etc/sudoers.d/persistent_password` et
l'éditer comme ceci :

```conf
# Timeout
Defaults timestamp_timeout=-1
```


SSH et les variables d'environnement
------------------------------------

Si l'on ne veut pas hériter des variables d'environnement du poste client, il
faut commenter toute cette section dans `/etc/ssh/sshd_config` :

```conf
# Accept locale-related environment variables
AcceptEnv LANG LC_CTYPE LC_NUMERIC LC_TIME LC_COLLATE LC_MONETARY LC_MESSAGES
AcceptEnv LC_PAPER LC_NAME LC_ADDRESS LC_TELEPHONE LC_MEASUREMENT
AcceptEnv LC_IDENTIFICATION LC_ALL LANGUAGE
AcceptEnv XMODIFIERS
```

Prendre en compte les modifications :

```console
$ sudo systemctl reload sshd
```

Activer le compte `root`
------------------------

Définir un mot de passe `root` pour activer le compte :

```console
$ sudo passwd root
Changing password for user root.
New password: ************
Retype new password: ************
passwd: all authentication tokens updated successfully.
```

Changer d'identité et devenir `root` :

```console
$ su -
```

> Ne pas oublier le `-` qui signifie que l'on devient `root` avec toutes les
> variables d'environnement de ce compte.


Configuration initiale du VPS (2)
=================================

  * Mise à jour initiale

  * État des lieux de l'installation

  * Dépôts de paquets officiels et tiers

  * Ajout du dépôt EPEL


Mise à jour initiale
--------------------

Lancer la mise à jour initiale :

```console
$ sudo dnf update
```

Si le noyau a été mis à jour :

```console
$ sudo reboot && exit
```

> Rappel sur les mises à jour et le cycle de support de Rocky Linux 8.


Dépôts et paquets - état des lieux
----------------------------------

Afficher les dépôts de paquets configurés :

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


Installer une panoplie d'outils
-------------------------------

Installer le groupe de paquets `Base` :

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

Installer quelques paquets qui ne sont pas fournis par le groupe de paquets
`Base` :

  * `elinks`

  * `git`

  * `glances`

  * `lynis`

  * `lynx`

  * `mailx`

  * `nmap`

Tester quelques commandes :

```console
$ glances
...
$ elinks www.microlinux.fr
...
$ lynx www.microlinux.fr
...
$ sudo nmap www.microlinux.fr
...
$ sudo lynis audit system
...
```


Peaufinage du *shell* Bash
--------------------------

Pour personnaliser le *shell* de l'utilisateur initial, je peux éditer un
fichier `~/.bashrc` comme ceci :

```bash
# ~/.bashrc

# Source global definitions 
if [ -f /etc/bashrc ]; then
  . /etc/bashrc
fi
 
# Alias
alias rm='rm -i'
alias cp='cp -i'
alias mv='mv -i'
alias ll='ls -al'
alias ..='cd ..'
alias ...='cd ../..'
alias vi='vim'

# PS1
GREEN='\[\033[0;32m\]'
WHITE='\[\033[1;37m\]'
NC='\[\033[0;m\]'
PS1="$GREEN[$WHITE\u$NC@$WHITE\h$NC:$WHITE\W$GREEN] \$ $NC"

# Vim
EDITOR=vim
VISUAL=$EDITOR
export EDITOR VISUAL
```

Pour prendre en compte cette configuration, je peux me déconnecter et me
reconnecter. Alternativement, je peux faire ceci :

```console
$ source ~/.bashrc
```

On va faire la même chose pour `root` mais avec un jeu de couleurs différent
pour l'invite de commandes :

```console
$ su -
Password: ************
```

Éditer un fichier `/root/.bashrc` comme ceci :

```bash
# /root/.bashrc

# Source global definitions
if [ -f /etc/bashrc ]; then
  . /etc/bashrc
fi
 
# LANG
LANG=en_US.utf8
export LANG

# Alias
alias rm='rm -i'
alias cp='cp -i'
alias mv='mv -i'
alias ll='ls -al'
alias ..='cd ..'
alias ...='cd ../..'
alias vi='vim'

# PS1
RED='\[\033[0;33m\]'
WHITE='\[\033[1;37m\]'
NC='\[\033[0m\]'
PS1="$RED[$WHITE\u$NC@$WHITE\h$NC:$WHITE\W$RED] #$NC "

# Vim
EDITOR=vim
VISUAL=$EDITOR
export EDITOR VISUAL
```

Prendre en compte ce fichier :

```console
# source .bashrc
# exit
```


Peaufinage de l'éditeur Vim
---------------------------

Pour une utilisation plus agréable de Vim, je crée un fichier `~/.vimrc` pour
mon utilisateur :

```vim
" ~/.vimrc
colorscheme elflord
set textwidth=79
set scrolloff=15
set autoindent
set tabstop=2
set shiftwidth=2
set expandtab
set modeline modelines=2
```

Et je vais faire de même pour `root` :

```console
$ su -
# vim .vimrc
# exit
```


Désactiver l'IPv6
-----------------

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

