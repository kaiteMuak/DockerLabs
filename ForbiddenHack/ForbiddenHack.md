ForbiddenHack - Writeup

#Resumen
Lorem Ipsum

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
Como la idea es bypassear el código 500 de la página web, tendremos que buscar algún parametro vulnerable para poder realizar un **RCE**, por lo que usamos `wfuzz` agregando `-H "Referer: http://bypass403.pw"`, esto ya que el servidor permite acceso únicamente si el `Referer` coincide con su propio dominio.
```
wfuzz -c -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt --hl 38 -u http://bypass403.pw?FUZZ=test -H "Referer: http://bypass403.pw"
```
<img width="586" height="174" alt="image" src="https://github.com/user-attachments/assets/ec093833-dcf3-4e2b-98cc-2647690c73bf" />

Encontramos que el parámetro vulnerable es `pages`, por lo que ahora podremos pasarlo a **Burpsuite** e interceptar la petición.

## Paso N4: Interceptando la petición
Entraremos a **Burpsuite** y mandamos la petición al **Repeater**, agregando nuevamente la cabecera `Referer: http://bypass403.pw` y probando el parámetro `pages`
<img width="1038" height="410" alt="image" src="https://github.com/user-attachments/assets/7fa76724-4431-4055-888f-04b3a16e192f" />

Vemos que funciona correctamente.



