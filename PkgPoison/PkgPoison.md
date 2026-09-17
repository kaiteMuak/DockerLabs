# PkgPoison - Writeup

## Resumen
Lorem ipsum

## Paso N1: Reconocimiento 
Empezamos escaneando los puertos TCP con sus respectivos servicios y versiones
```
nmap 172.17.0.2 -sS -sVC -n -Pn -p- --open --min-rate 5000 -oG open_ports
```
y vemos que están los puertos 22 y 80 correspondientes a ssh y http respectivamente. 
