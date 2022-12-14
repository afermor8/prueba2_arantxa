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
Para definir las claves, ejecutar **desde el directorio /etc/tripwire** el siguiente comando:

`sudo twadmin --generate-keys --site-keyfile site.key --site-passphrase 'INSERTAR LA CONTRASEÑA'`

`sudo twadmin --generate-keys --local-keyfile $(hostname_del_equipo)-local.key --local-passphrase 'INSTRAR LA CONTRASEÑA'`

Si ya estuvieran configuradas las claves pasamos directamente a este paso. Generamos los ficheros tw.cfg y tw.pol del siguiente modo:

`sudo twadmin -m F -S /etc/tripwire/site.key /etc/tripwire/twcfg.txt`

`sudo twadmin -m P -S /etc/tripwire/site.key /etc/tripwire/twpol.txt`

![crear-ficheros](/capturas/2tripwire-fich.png)

## Creación de la base de datos

Creamos la base de datos.

`sudo tripwire -m i`

![crear-bd](/capturas/3tripwire-bd.png)

## Comprobación de la integridad de ficheros

Se puede realizar una comprobación con el siguiente comando:

`sudo tripwire -m c`

![scan](/capturas/4tripwire-int.png)

![scan2](/capturas/4tripwire-int2.png)

![scan3](/capturas/4tripwire-int3.png)

El informe detallado de la comprobación se puede ver con:

`sudo twprint -m r -r /var/lib/tripwire/report/debianprueba-20221016-195327.twr`

> -m r indica a tripwire que descifre el informe y -r el nombre del informe a descifrar
> debianprueba-20221016-195327 es el nombre del informe creado. Se puede entrar en el directorio /var/lib/tripwire/report/ para ver el nombre del informe que se haya creado en tu máquina.


## Actualización de la base de datos

Para actualizar la base de datos y acepte las modificaciones que son legítimas (las que hemos hecho nosotros por ejemplo) se debe usar el siguiente comando.

`sudo tripwire -m u -r /var/lib/tripwire/report/debianprueba-20221016-195327.twr` 

Podemos marcar qué cambios queremos aceptar y cuáles no queremos cambiar en la base de datos.

![update](/capturas/5tripwire-update.png)

Al salir se guardan los cambios (nos pide la clave local).

![save-updates](/capturas/6.tripwire-cambios-guardados.png)

Para ver todo el contenido de la base de datos:

`sudo twprint -m d -d /var/lib/tripwire/debianprueba.twd`

Con el comando anterior nos aparecerá muchísima información por lo que podemos ver solo la información de un fichero de la base de datos de la siguiente forma.

`sudo twprint -m d -d /var/lib/tripwire/debianprueba.twd nombre_fichero`

![ejemplo](/capturas/7tripwire-ej.png)

## Avisos por correos electrónicos

Antes debemos saber que el servidor de correo por defecto en Tripwire es Postfix. Tendremos que configurarlo para que nos permita mandar correos electrónicos. 
Instalamos postfix y mailutils  si no los tenemos instalados.

`sudo apt install postfix mailutils` 

En el directorio **/etc/postfix** creamos sasl_passwd.

`sudo nano /etc/postfix/sasl_passwd`

Añadimos al fichero creado lo siguiente para un servidor Gmail:

`[smtp.gmail.com]:587 correo@gmail.com:contraseña-correo`

Establecemos la seguridad para este fichero:

`sudo chmod 600 /etc/postfix/sasl_passwd`

`sudo chown root:root /etc/postfix/sasl_passwd`

Creamos el fichero tls_policy.

`sudo nano /etc/postfix/tls_policy`

Le añadimos lo siguiente para un servidor Gmail.

`[smtp.gmail.com]:587 encrypt`

Le damos a este fichero los siguientes permisos:

`sudo chmod 600 /etc/postfix/tls_policy`

`sudo chown root:root /etc/postfix/tls_policy`

Añadimos la configuración a main.cf.

`sudo nano /etc/postfix/main.cf`

```
myhostname = debianprueba
relayhost = [smtp.gmail.com]:587
smtp_sasl_auth_enable = yes
smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd
smtp_tls_policy_maps = hash:/etc/postfix/tls_policy
smtp_sasl_security_options = noanonymous
smtp_use_tls = yes
smtp_tls_CAfile = /etc/ssl/certs/ca-certificates.crt
smtp_tls_security_level = encrypt
```

Se convierten lo dos ficheros a hash. Se crearán los ficheros sasl_passwd.db y tls_policy.db:

`sudo postmap /etc/postfix/sasl_passwd`

`sudo postmap /etc/postfix/tls_policy`

Se reinicia el proceso:

`sudo systemctl restart postfix`

Y ya debería funcionar. Podemos hacer una prueba para comprobarlo.

`echo "prueba de envío" | mail -s "Esto es una prueba" ara.fer.mor@gmail.com`

![prueba-mail](/capturas/prueba-mail.png)


### Correo desde Tripwire.

En el fichero de configuración de las políticas twpol.txt se debe añadir la opción **emailto = correo@correo.com**.

![twpol-emailto](/capturas/8tripwire-mail.png)

Y en el fichero *twcfg.txt* se debe añadir el **SMTPHOST** y **SMTPPORT**, quedando de la siguiente manera:

```
MAILMETHOD    =SENDMAIL
SMTPHOST      =smtp.gmail.com
SMTPPORT      =587
MAILPROGRAM   =/usr/lib/sendmail -oi -t
```

Una vez hecho estos cambios se debe generar de nuevo los ficheros tw.pol y tw.cfg.

`sudo twadmin -m P -S /etc/tripwire/site.key /etc/tripwire/twpol.txt`

`sudo twadmin -m F -S /etc/tripwire/site.key /etc/tripwire/twcfg.txt`

Para comprobar que funciona usar el siguiente comando para enviar un mensaje de prueba.

`sudo tripwire -m t -e ara.fer.mor@gmail.com`

![prueba-recibida](/capturas/tripwire-mail.png)


## Tripwire desatendida

Mediante Crontab podemos hacer que nos se hagan los chequeos automáticamente y se nos envíe un correo del siguiente modo.

`sudo crontab -e -u root`

Y añadimos la siguiente línea:

`0 0 * * * tripwire --check --email-report`



## Páginas webs consultadas

http://informatica.uv.es/it3guia/AGR/apuntes/teoria/documentos/Tripwire.pdf

https://blog.inittab.org/administracion-sistemas/monitorizacion-de-cambios-en-ficheros-con-tripwire-i/

https://www.etl.it.uc3m.es/Manual_de_configuraci%C3%B3n_y_uso_de_Tripwire

