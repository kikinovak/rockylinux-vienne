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

On va créer notre utilisateur initial.

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


