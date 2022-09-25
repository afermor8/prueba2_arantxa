# Git y Github
## Creación de un repositorio local

Así he creado el *repositorio local* con **git**:

`arantxa@debian:~$ git init prueba2_arantxa`

`arantxa@debian:~$ cd prueba2_arantxa/`

`arantxa@debian:~/prueba2_arantxa$ nano creacion.txt`

`arantxa@debian:~/prueba2_arantxa$ git add .`

`arantxa@debian:~/prueba2_arantxa$ git commit -m "He creado un archivo con el proceso de creacion"`

Creo un repositorio en **Github** con el mismo nombre (prueba2_arantxa) y realizo los siguientes pasos:

`arantxa@debian:~/prueba2_arantxa$ git branch -M main`

`arantxa@debian:~/prueba2_arantxa$ git remote add origin git@github.com:afermor8/prueba2_arantxa.git`

`arantxa@debian:~/prueba2_arantxa$ git push -u origin main`


Y ya estaría creado en Github sincronizado con el repositorio local.

![alt text](capturagitlocal.png)

Click [aquí](https://github.com/afermor8/prueba2_arantxa) para ir al enlace del repositorio creado en Github.
