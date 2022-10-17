# Rootkit Hunter (rkhunter).

## Instalación

Rkhunter es un script de shell disponible en sistemas operativos de tipo UNIX, como GNU/Linux y sus distribuciones. Esta herramienta analiza los sistemas buscando la presencia de rootkits, backdoors, sniffers y exploits conocidos y desconocidos.

Comprueba:

- Los cambios de hash SHA256
- Los archivos comúnmente creados por rootkits
- Los ejecutables con permisos de archivos anómalos
- Las cadenas sospechosas en los módulos del kernel
- Los archivos ocultos en los directorios del sistema
- Y, opcionalmente, puede escanear dentro de los archivos

Usar rkhunter solo no garantiza que un sistema no se vea comprometido. Se recomienda ejecutar pruebas adicionales, como chkrootkit.

Para instalar rkhunter:

`sudo apt install rkhunter`

Con la opción *-h* podemos ver las opciones de rkhunter.




## Configuración

El archivo de configuración de rkhunter se ubica en /etc/rkhunter.conf.

`sudo nano /etc/rkhunter.conf`

### Opciones de configuración

- Mirrors.  
Para que rkhunter use cualquier mirror he cambiado la opción **MIRRORS_MODE** a "0".
La opción **UPDATE_MIRRORS** la he puesto en "1" para que cuando se utilice --update el fichero mirrors sea actualizado si es necesario.

![mirrors](/capturas/3rkhunter-mirrors.png)

- Web_cmd.  
Esta opción está por defecto del siguiente modo: `WEB_CMD="/bin/false"`
Lo he cambiado a `WEB_CMD=""` para que permita a rkhunter hacer actualizaciones de los mirrors. La opción por defecto bloquea el acceso a los mirror por razones de seguridad.

- Correo electrónico.  
Para que rkunter envíe un mensaje si se encuentra con una advertencia durante la comprobación del sistema habrá que añadir a la opción **MAIL-ON-WARNING=** un correo de contacto (o varios separándolos con un espacio).La opción **"MAIL_CMD"** debe estar descomentada y poner lo siguiente.

`MAIL_CMD=mail -s "[rkhunter] Warnings found for ${HOST_NAME}" ara.fer.mor@gmail.com`

![mail-cmd](/capturas/10rkhunter-mail.png)

Al escanear el sistema, si hubiera Warnings nos llegaría el siguiente mensaje:

![prueba-mail](/capturas/rkhunter-mail2.png)

Si se realizan cambios en la configuración se debe usar el siguiente comando para que los cambios se apliquen.

`sudo rkhunter -C`

ó

`sudo rkhunter --config-check`




## Actualizaciones

Para actualizar los archivos de las bases de datos:

`sudo rkhunter --update`

*La base de datos del rkhunter se actualiza cada semana de forma automática.

Para actualizar las propiedades de los archivos:

`sudo rkhunter --propupd`

![update-propupd](/capturas/4rkhunter-update.png)



## Escanear el sistema

Para iniciar el escaneo:

`sudo rkhunter -c`

ó

`sudo rkhunter --check`

![scan-rkhunter](/capturas/5rkhunter-scan.png)

Para que, durante el escaneo, nos aparezcan solo los Warning podemos usar:

`sudo rkhunter -c --rwo`

![rwo](/capturas/9rkhunter-rwo.png)

Cuando finalice la primera etapa de escaneo de malware en los binarios presionamos *Enter*. Continua con la búsqueda de rootkits en el sistema de archivos. Cada vez que termine una etapa deberemos confirmar presionando *Enter*. Las siguientes etapas son escaneos adicionales sobre el sistema de archivos y módulos del kernel en búsqueda de backdoors, rootkits y malwares en general, y el escaneo de las redes y localhost.

![enter](/capturas/6rkhunter-enter.png)

Si queremos que no nos vaya preguntando podemos usar la opción:

`rkhunter -c -sk`

Cuando termine nos aparecerán los resultados de la búsqueda.

![results](/capturas/7rkhunt-results.png)

Toda la información del proceso de escaneo se guardará en el fichero **/var/log/rkhunter.log**.

`sudo cat /var/log/rkhunter.log`

Usando grep podemos por ejemplo ver solo la información de los Warnings del resultado del escaneo.

`sudo grep -i warning /var/log/rkhunter.log`

![warnings](/capturas/8rkhunter-warning.png)



## Actualizaciones y análisis desatendidos

- Fichero **/etc/default/rkhunter**.  
Desde el fichero */etc/default/rkhunter*, Cron ejecutará el script todos los días para escanear y actualizar. Se pueden habilitar las comprobaciones de análisis de Rkhunter configurando **CRON_DAILY_RUN** en "yes" para que se ejecute diariamente. También se puede establecer **CRON_DB_UPDATE** para habilitar las actualizaciones semanales de la base de datos. Otra opción es establecer el valor de **APT_AUTOGEN** si se desea habilitar las actualizaciones automáticas de la base de datos.

![cron-rkhunter](/capturas/2rkhunter-cron.png)

- Crontab:  
Desde Crontab podemos hacer que las actualizaciones y los escaneos se hagan de forma automática todos los días a cierta hora editando Crontab para añadir la tarea.

`sudo crontab -u root -e`

Y añadimos en la última línea nuestras preferencias, en este caso en el minuto 0 a las 00 horas todos los días, meses y días de la semana hará dos tareas, actualizar (rkhunter --cronjob --update --quiet) y escanear (rkhunter --cronjob -c --enable all --quiet).

`0 00 * * * rkhunter --cronjob --update --quiet; rkhunter --cronjob -c --quiet`

