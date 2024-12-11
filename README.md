# Rocky Linux 9 "aux petits oignons"

Ce document décrit pas à pas et de manière succincte l'installation et la
configuration d'un poste de travail Linux "aux petits oignons" basé sur Rocky
Linux 9 et l'environnement de bureau KDE.

Atelier pratique réalisé avec mes stagiaires à Vienne Condrieu Agglomération en
décembre 2024.

## Téléchargement

Télécharger l'ISO Minimal sur [le site de Rocky
Linux](https://www.rockylinux.org):

- `Rocky-9.5-x86_64-minimal.iso`

Pour confectionner le support d'installation, on utilisera un système Linux
existant. 

Insérer et identifier la clé USB :

```
# lsblk
```

Écrire le fichier ISO sur la clé USB :

```
# dd status=progress if=Rocky-9.5-x86_64-minimal.iso of=/dev/sdX
```

> Attention à ne pas vous tromper de fichier de périphérique !


## Réinitialiser le disque

Démarrer sur la clé en choisissant l'option `Rescue a Rocky Linux System`.

Opter pour l'option `3) Skip to shell` et confirmer.

Passer du clavier QWERTY à un clavier AZERTY :

```
# loadkeys fr-latin1
```

Pour réinitialiser le disque, lancez `gdisk` et utilisez successivement les
options `x` et `z`.


## Partitionnement

Identifiez le disque à partitionner :

```
# lsblk
```

Exemple avec BIOS/MBR (`fdisk`) :

```
  Device     Boot    Start       End   Sectors  Size Id Type
  /dev/sda1  *        2048   2099199   2097152    1G 83 Linux
  /dev/sda2        2099200  10487807   8388608    4G 82 Linux swap
  /dev/sda3       10487808 117231407 106743600 50.9G 83 Linux
```

Exemple avec BIOS/GPT (`gdisk`) :

```
  Device        Start       End   Sectors  Size Type
  /dev/sda1      2048      4095      2048    1M BIOS boot
  /dev/sda2      4096   2101247   2097152    1G Linux filesystem
  /dev/sda3   2101248  10489855   8388608    4G Linux swap
  /dev/sda4  10489856 117229567 106739712 50.9G Linux filesystem
```

Exemple avec UEFI/GPT (`gdisk`) :

```
  Device        Start       End   Sectors  Size Type
  /dev/sda1      2048    206847    204800  100M EFI System
  /dev/sda2    206848   2303999   2097152    1G Linux filesystem
  /dev/sda3   2304000  10692607   8388608    4G Linux swap
  /dev/sda4  10692608 117229567 106536960 50.8G Linux filesystem
```

> Idéalement, créez une partition `swap` égale à la quantité de RAM disponible
> sur votre machine. Utilisez la commande `free -m` pour en savoir plus.


## Installation

- Démarrez sur le support d'installation.

- Sélectionnez la langue et le clavier.

- Optez pour le partitionnement manuel et formatez les partitions que vous
  venez de faire. Pour les étiquettes (*labels*) vous pouvez choisir `EFI`,
  `boot`, `swap` et `root`.

- Activez le réseau et vérifiez si vous obtenez bien une adresse IP. 

- Choisissez un nom d'hôte en remplacement de `localhost.localdomain`.

- Configurez le fuseau horaire.

- Désactivez Kdump (mécanisme de capture de plantage du noyau).

- Dans la sélection des paquets, optez pour **Installation minimale**.

- Définissez le mot de passe `root`.

- Créez un utilisateur normal, par exemple `microlinux`.

- Cochez la case **Faire de cet utilisateur un administrateur**. L’utilisateur
  sera ajouté au groupe `wheel` et pourra se servir de la commande `sudo`.

- Lancez l'installation.


## Mise à jour

Au terme de l'installation, connectez-vous en tant que `root` et effectuez la
mise à jour initiale :

```
# dnf update -y
```

Redémarrez :

```
# reboot
```

## Fichiers et documentation

Installez Git :

```
# dnf install -y git
```

Récupérez les fichiers de cet atelier pratique dans `/root` :

```
# cd
# git clone https://github.com/kikinovak/rockylinux-vienne
# cd rockylinux-vienne
```

## Outils de base

Le groupe `Base` contient une panoplie d'outils en ligne de commande
raisonnablement complète.

Afficher le groupe :

```
# dnf group list hidden | grep -i base
```

Installer le groupe :

```
# dnf group install Base
```

## Personnaliser le shell Bash

Installer le fichier `.bashrc` pour `root` .

```
# cp -vf bash/bashrc-root /root/.bashrc
```

Installer le fichier `.bashrc` pour l'utilisateur initial (`microlinux` dans
l'exemple) :

```
# cp -vf bash/bashrc-user /home/microlinux/.bashrc
# chown microlinux:microlinux /home/microlinux/.bashrc
```

Installer le fichier `.bashrc` pour les futurs utilisateurs :

```
# cp -vf bash/bashrc-user /etc/skel/.bashrc
```

## Personnaliser l'éditeur Vim

Installer le fichier `.vimrc` pour `root` .

```
# cp -vf vim/vimrc /root/.vimrc
```

Installer le fichier `.vimrc` pour l'utilisateur initial (`microlinux` dans
l'exemple) :

```
# cp -vf vim/vimrc /home/microlinux/.vimrc
# chown microlinux:microlinux /home/microlinux/.vimrc
```

Installer le fichier `.vimrc` pour les futurs utilisateurs :

```
# cp -vf vim/vimrc /etc/skel/.vimrc
```

## Configurer le dépôt de paquets EPEL

Le dépôt de paquets tiers EPEL (*Extra Packages for Enterprise Linux*) fournit
un grand nombre de paquets logiciels qui ne sont pas officiellement inclus dans
RHEL et ses clones.

Activer le dépôt EPEL :

```
# dnf install -y epel-release
```

Ce dépôt nécessite l'activation du dépôt CRB (*Code Ready Builder*) :

```
# /usr/bin/crb enable
```

Afficher la liste des dépôts configurés :

```
# dnf repolist
```


## Configurer le dépôt de paquets ELRepo

Le dépôt de paquets tiers ELRepo (*Enterprise Linux Repository*) fournit
surtout des kernels plus récents et toute une série de pilotes (ou *drivers*)
pour RHEL et ses clones.

Activer le dépôt ELRepo :

```
# dnf install -y elrepo-release
```

Afficher la liste des dépôts configurés :

```
# dnf repolist
```

## Configurer les dépôts de paquets RPMFusion

Les quatre dépôts RPMFusion fournissent des paquets potentiellement
problématiques en termes de licence (multimédia, paquets propriétaires, etc.)

Activer le dépôt RPMFusion Free : 

```
# dnf install --nogpgcheck \
  https://mirrors.rpmfusion.org/free/el/rpmfusion-free-release-9.noarch.rpm
```

Activer le dépôt RPMFusion Nonfree : 

```
# dnf install --nogpgcheck \
  https://mirrors.rpmfusion.org/nonfree/el/rpmfusion-nonfree-release-9.noarch.rpm
```

Activer le dépôt RPMFusion Free Tainted :

```
# dnf install -y rpmfusion-free-release-tainted
```

Activer le dépôt RPMFusion Nonfree Tainted:

```
# dnf install -y rpmfusion-nonfree-release-tainted
```

Afficher la liste des dépôts configurés :

```
# dnf repolist
```

## Configurer le dépôt de paquets Google Chrome

Éditer un fichier `/etc/yum.repos.d/google-chrome.repo` comme ceci :

```
[chrome]
name=Chrome
baseurl=http://dl.google.com/linux/chrome/rpm/stable/x86_64
enabled=1
priority=10
gpgcheck=1
gpgkey=https://dl.google.com/linux/linux_signing_key.pub
```

Si tout se passe bien, une recherche sur `chrome` doit afficher quelque chose
comme ceci :

```
# dnf search chrome
...
google-chrome-beta.x86_64 : Google Chrome (beta)
google-chrome-canary.x86_64 : Google Chrome (canary)
google-chrome-stable.x86_64 : Google Chrome
google-chrome-unstable.x86_64 : Google Chrome (unstable)
```


## Installer KDE

Dans un premier temps, installer le paquet `ffmpeg` en provenance de RPMFusion.
Ce paquet est une dépendance de KDE sous le capot, et de cette manière on est
sûr de gérer correctement tous les formats audio et vidéo.

``` 
# dnf install -y ffmpeg 
``` 

Partant de là, on peut installer le groupe de paquets pour KDE :

``` 
# dnf group install "KDE (K Desktop Environment)"
``` 

Démarrer en mode graphique par défaut :

```
# systemctl set-default graphical.target
```

## Franciser le système

Il se peut que le système n'utilise pas la bonne locale :

```
# localectl status
System Locale: LANG=C.UTF-8
    VC Keymap: ch-fr
   X11 Layout: ch
  X11 Variant: fr
```

Dans ce cas, on peut définir la langue française par défaut pour le système :

```
# localectl set-locale LANG=fr_FR.UTF-8
```

Vérifier si tout s'est bien passé :

```
# localectl status
System Locale: LANG=fr_FR.UTF-8
    VC Keymap: ch-fr
   X11 Layout: ch
  X11 Variant: fr
```

## Configuration initiale de KDE

Dans les Paramètres de KDE, ouvrir la section **Gestion de l'énergie** et
désactiver tout ce qui ressemble à de la mise en veille.

Faire une recherche sur `background` et `wallpaper` pour trouver une collection
de fonds d'écran :

```
# dnf search background
# dnf search wallpaper
```

## Applications Internet

- Mozilla Firefox : `firefox`

- Google Chrome : `google-chrome-stable`

- Mozilla Thunderbird : `thunderbird`

- Filezilla : `filezilla`

- Client BitTorrent : `transmission`

- Client IRC : `hexchat`

- Client VNC : `krdc`

