# Tripwire. Control de integridad de ficheros.

Tripwire es un programa que asegura la integridad de ficheros y directorios obteniendo el estado inicial de los ficheros y directorios del sistema y comparando a posteriori si han habido cambios con respecto a ese estado inicial.
Se informa de los cambios detectados al administrados por correo electrónico.

Esta herramienta nos puede ayudar a detectar intrusiones en el sistema al informarnos de modificaciones que no ha realizado ni el administrador ni los usuarios permitidos. Se minimiza el impacto de esta intrusión al conocer los ficheros o directorios en los que se han realizado cambios y pudiendo restaurarlos.

Tripwire genera una base de datos inicial con toda la información de los ficheros y directorios. Periódicamente se genera otra base de datos con el estado actual y la compara con el estado inicial para informar de los cambios (modificaciones, adiciones o eliminaciones).


## Instalación del paquete Tripwire en Debian

`sudo apt install tripwire`


## Configuración inicial

### Definición de las claves

Tripwire tiene dos claves para almacenar su información (se encuentran en /etc/tripwire/). Se pueden definir posteriormente a la instalación o durante la instalación en Debian:
- site.key → clave del sitio para encriptar los ficheros de configuración y políticas, evitando cambios no autorizados.
- $HOSTNAME-local.key → clave local para encriptar la base de datos.


### Ficheros de configuración

Tripwire tiene dos ficheros de configuración.
- /etc/tripwire/twcfg.txt → Contiene los paths (directorios y ficheros) donde tripwire almacena la base de datos y la configuración para el envío de correo con los informes periódicos. Al instalarse tripwire se configura este fichero por defecto y no es necesario modificarlo. Si se modifica en los valores iniciales siempre se debe especificar un valor para que no de error. El fichero de configuración debe contener como mínimo lo siguiente:

```
ROOT =/usr/sbin 				#Directorio donde se encuentran los ejecutables de tripwire
POLFILE =/etc/tripwire/tw.pol 			#Ubicación del archivo de políticas
DBFILE =/var/lib/tripwire/$(HOSTNAME).twd 	#Localización del archivo de la base de datos
REPORTFILE =/var/lib/tripwire/report/$(HOSTNAME)-$(DATE).twr 	#Ubicación de los archivos de informes
SITEKEYFILE =/etc/tripwire/site.key 		#Ubicación del archivo de la clave del sitio
LOCALKEYFILE =/etc/tripwire/$(HOSTNAME)-local.key #Ubicación del archivo de la clave local
EDITOR =/bin/vi 				#Editor de texto que ejecutará tripwire
LATEPROMPTING =false 				#Indica que se espere lo máx posible antes de preguntar la contraseña al usuario
LOOSEDIRECTORYCHECKING =false 			#Informa sobre los cambios que se han realizado en un archivo en un directorio y no sobre los cambios del propio directorio para reducir la redundancia
MAILNOVIOLATIONS =true 				#Manda un correo de forma periódica aunque no haya ninguna intrusiń en el sistema
EMAILREPORTLEVEL =3 				#Nivel de detalle para los informes enviados por correo electrónico
REPORTLEVEL =3 					#Nivel de detalle para los informes generados con el comando twprint
MAILMETHOD =SENDMAIL 				#Especifica el protocolo de correo que usará tripwire
SYSLOGREPORTING =false 				#Indica a tripwire que informe al demonio syslog d elos cambios
MAILPROGRAM =/usr/sbin/sendmail -oi -t 		#Especifica el programa de correo que usará tripwire
```

- /etc/tripwire/twpol.txt → Contiene las políticas de tripwire. En el fichero habrá comentarios (se marcan con el carácter #), reglas (con qué severidad serán analizados o no los ficheros y directorios), directivas (órdenes que permiten condicionar la interpretación de la política de tripwire) y variables (definir cadenas de texto para su sustitución en el fichero).
Al instalar tripwire se crea por defecto una plantilla en este fichero que se puede modificar según nuestras preferencias.
La sintaxis de las reglas es _nombre-del-objeto -> mascara-de-propiedades;_. La máscara  especifica qué propiedades del objeto serán examinadas o ignoradas. Se compone de carácteres que pueden ser los que se ven a continuación y puede ir precedida de un signo + o -.

|Carácter	|Propiedad a comprobar o ignorar	|
|---------------|---------------------------------------|
|a 		|Fecha y hora de acceso.		|
|b 		|Número de bloques utilizados.		|
|c 		|Fecha y hora de creación o modificación de los inodos.|
|d 		|Identificador del dispositivo donde los inodos se encuentran.|
|g 		|Identificador del grupo del fichero.	|
|i 		|Número de inodos.			|
|l 		|El fichero ha aumentado su tamaño	|
|m 		|Fecha y hora de modificación.		|
|n 		|Número de enlaces (contador de referencias del inodo).|
|p 		|Permisos y bits de modo del fichero.	|
|r 		|Identificador del dispositivo apuntado por el inodo (valido solo para objetos que se refieran a un dispositivo).|
|s 		|Tamaño del fichero.			|
|t 		|Tipo del fichero.			|
|u 		|Identificador del dueño del fichero.	|
|C 		|Valor hash del CRC-32 del fichero.	|
|H 		|Valor hash de Haval (firma de 128 bits) del fichero.|
|M 		|Valor hash del MD5 del fichero.	|
|S 		|Valor hash del SHA del fichero.	|

Las variables, usadas para organizar mejor la información, se usan de la siguiente forma:

```
asd = +pinugtsdbmCM-rlacSH;
 #Usarlo en reglas de la siguiente forma
/etc/passwd -> $(asd);
```

Algunas variables ya predefinidas son _ReadOnly_, _Dynamic_, _Growing_, _Device_, _IgnoreAll_ o _IgnoreNone_.

También se pueden hacer agrupaciones de ficheros o directorios.

```
(lista-atributos)
{
	lista-reglas;
}
```

Los atributos pueden ser rulename, emailto, severity o recurse.
La sintaxis de las directivas es la siguiente:

`@@nombre-directiva [argumentos]`

El nombre de la directiva será _section_, _ifhost...else...endif_, _print_, _error_ o _end_.

![twpol](/capturas/1tripwire-twpol.png)


## Instalación del fichero de políticas y de configuración

En el caso de que la configuración de los ficheros y las claves no se haya hecho durante la instalción en Debian se deberá hacer manualmente.
Para definir las claves, ejecutar desde el directorio /etc/tripwire el siguiente comando:

`sudo twadmin --generate-keys --site-keyfile site.key --site-passphrase 'INSERTAR LA CONTRASEÑA'`

`sudo twadmin --generate-keys --local-keyfile $(hostname_del_equipo)-local.key --local-passphrase 'INSTRAR LA CONTRASEÑA'`

Generar los ficheros tw.cfg y tw.pol:

`sudo twadmin -m P /etc/tripwire/twpol.txt`

`sudo twadmin -m F /etc/tripwire/twcfg.txt`

## Creación de la base de datos

Creamos la base de datos e indicamos que se guarde en un fichero si se produce un error.

`sudo tripwire -m -i 2 > /tmp/mensaje_si_error`

## Comprobación de la integridad de ficheros

Se puede realizar una comprobación con el siguiente comando:

`sudo tripwire -m c`

Y si nos menciona algún fichero o directorio se puede ver el informe detallado con:

`sudo twprint -m r -r /var/lib/tripwire/report/<nombre>.twr`

> -m r indica a tripwire que descifre el informe y -r el nombre del informe a descifrar


## Actualización de la base de datos

Para actualizar la base de datos y acepte las modificaciones que son legítimas (las que hemos hecho nosotros por ejemplo) se debe usar el siguiente comando.

`sudo tripwire -m u -r /var/lib/tripwire/report/<nombre>.twr` 

Podemos ver la información de un fichero de la base de datos de la siguiente forma.

`sudo twprint -m d -d /var/lib/tripwire/<nombre>.twd <fichero>`

Para ver todo el contenido de la base de datos:

`sudo twprint -m d -d /var/lib/tripwire/<nombre>.twd`


## Páginas webs consultadas

http://informatica.uv.es/it3guia/AGR/apuntes/teoria/documentos/Tripwire.pdf

https://blog.inittab.org/administracion-sistemas/monitorizacion-de-cambios-en-ficheros-con-tripwire-i/

https://www.etl.it.uc3m.es/Manual_de_configuraci%C3%B3n_y_uso_de_Tripwire

