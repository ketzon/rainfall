# Explication

Quand on execute le binaire sans parametre celui ci va segfault, en passant un parametre il renvoi `No !`

On va utiliser Ghidra pour reverse engine le binaire `level0`.
Ghidra nous est un outil qui nous permet facilement de decompiler un binaire.

On creer un projet, import le binaire et ensuite on peut aller regarder les fonctions sur la gauche, on tombe sur la fonction main qui fait un `atoi` de notre parametre et le compare est une `0x1a7` qui en decimal vaut `423`. Apres avoir ecrit `423` le main va lancer un shell avec le user `level1`, shell avec lequel on peut interagir et aller chercher le flag ce situant
`/home/user/level1`.

```sh
$ cd /home/user/level1
$ ls -la
total 17
dr-xr-x---+ 1 level1 level1   80 Mar  6  2016 .
dr-x--x--x  1 root   root    340 Sep 23  2015 ..
-rw-r--r--  1 level1 level1  220 Apr  3  2012 .bash_logout
-rw-r--r--  1 level1 level1 3530 Sep 23  2015 .bashrc
-rwsr-s---+ 1 level2 users  5138 Mar  6  2016 level1
-rw-r--r--+ 1 level1 level1   65 Sep 23  2015 .pass
-rw-r--r--  1 level1 level1  675 Apr  3  2012 .profile
$ cat .pass
1fe8a524fa4bec01ca4ea2a869af2a02260d4a7d5fe7e7c24d8617e6dca12d3a
$
```
