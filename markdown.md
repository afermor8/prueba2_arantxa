# Git y Github
## Creación de un repositorio local

Así he creado el *repositorio local* con **git**:

`arantxa@debian:~$ git init prueba2_arantxa`
`arantxa@debian:~$ cd prueba2_arantxa/`
`arantxa@debian:~/prueba2_arantxa$ nano creacion.txt`
`arantxa@debian:~/prueba2_arantxa$ git add .`
`arantxa@debian:~/prueba2_arantxa$ git commit -m "He creado un archivo con el proceso de creacion"`

Creo un repositorio en **Github** con el mismo nombre (prueba2_arantxa) y realizo los siguientes pasos:

arantxa@debian:~/prueba2_arantxa$ git branch -M main

arantxa@debian:~/prueba2_arantxa$ git remote add origin git@github.com:afermor8/prueba2_arantxa.git

arantxa@debian:~/prueba2_arantxa$ git push -u origin main
Enumerando objetos: 3, listo.
Contando objetos: 100% (3/3), listo.
Escribiendo objetos: 100% (3/3), 272 bytes | 272.00 KiB/s, listo.
Total 3 (delta 0), reusado 0 (delta 0), pack-reusado 0
To github.com:afermor8/prueba2_arantxa.git
 * [new branch]      main -> main
Rama 'main' configurada para hacer seguimiento a la rama remota 'main' de 'origin'.


Y ya estaría creado en Github sincronizado con el repositorio local.

![alt text](capturagitlocal.png)

El enlace al repositorio es el siguiente:
[title](https://github.com/afermor8/prueba2_arantxa)
