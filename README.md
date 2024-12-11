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
existant. Insérer et identifier la clé USB :

```
# lsblk
```

Écrire le fichier ISO sur la clé USB :

```
# dd status=progress if=Rocky-9.5-x86_64-minimal.iso of=/dev/sdX
```

