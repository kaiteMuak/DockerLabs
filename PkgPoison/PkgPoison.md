# PkgPoison - Writeup

## Resumen
Lorem ipsum

## Paso N1: Reconocimiento 
Empezamos escaneando los puertos TCP con sus respectivos servicios y versiones
```
nmap 172.17.0.2 -sS -sVC -n -Pn -p- --open --min-rate 5000 -oG open_ports
```
y vemos que están los puertos 22 y 80 correspondientes a ssh y http respectivamente. 
![]()

## Paso N2: Descubrimiento de subdirectorios
En la página inicial no encontraremos gran cosa, por lo que empezamos la busqueda de subdirectorios en busca de información.
```
gobuster dir -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -u http://172.17.0.2/ -x php,html
```
![]()
