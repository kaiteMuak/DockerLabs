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
