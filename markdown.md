# Git y Github
## Creación de un repositorio local

Así he creado el **repositorio local** con *git*:

'arantxa@debian:~$ git init prueba2_arantxa
ayuda: Using 'master' as the name for the initial branch. This default branch name
ayuda: is subject to change. To configure the initial branch name to use in all
ayuda: of your new repositories, which will suppress this warning, call:
ayuda: 
ayuda: 	git config --global init.defaultBranch <name>
ayuda: 
ayuda: Names commonly chosen instead of 'master' are 'main', 'trunk' and
ayuda: 'development'. The just-created branch can be renamed via this command:
ayuda: 
ayuda: 	git branch -m <name>
Inicializado repositorio Git vacío en /home/arantxa/prueba2_arantxa/.git/

arantxa@debian:~$ cd prueba2_arantxa/

arantxa@debian:~/prueba2_arantxa$ nano creacion.txt

arantxa@debian:~/prueba2_arantxa$ git add .

arantxa@debian:~/prueba2_arantxa$ git commit -m "He creado un archivo con el proceso de creacion"
[master (commit-raíz) ee32a6f] He creado un archivo con el proceso de creacion
 1 file changed, 1 insertion(+)
 create mode 100644 creacion.txt
'

Creo un repositorio en Github con el mismo nombre y hago los siguientes pasos:

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
