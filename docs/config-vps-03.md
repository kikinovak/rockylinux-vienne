Configuration initiale du VPS (3)
=================================

  * Peaufinage du *shell* Bash

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

