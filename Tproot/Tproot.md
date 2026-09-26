# Tproot - Writeup

## Resumen
Lorem Ipsum

## Paso N1: Reconocimiento
Empezaremos escaneando los puertos TCP abiertos de la maquina victima con sus respectivos servicios y versiones
```
nmap 172.17.0.2 -sS -sVC -n -Pn -p- --open --min-rate 5000 -oG open_ports
```
<img width="669" height="373" alt="image" src="https://github.com/user-attachments/assets/2791bbe8-4cfb-46e9-9a64-575f622aa6a4" />

Podemos ver en pantalla que están abiertos los puertos 21 y 80 correspondientes a ftp y http respectivamente. Podemos ver que el servidor ftp está usando ```vsftpd 2.3.4```, una versión un poco antigua que tiene cierta vulnerabilidad.

## Paso N2:
