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

