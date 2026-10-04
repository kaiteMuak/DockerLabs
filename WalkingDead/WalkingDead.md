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
