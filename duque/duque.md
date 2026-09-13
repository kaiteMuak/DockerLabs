# duque - Writeup

## Resumen
lorem ipsum

## Paso N1: Reconocimiento
Empezamos escaneando los puertos TCP de la máquina y sus determinadas versiones y servicios correspondientes.
```
nmap 172.17.0.2 -sS -sVC -n -Pn -p- --open -oG open_ports
```
![]()
Podemos ver que estan abiertos los puertos TCP 22 y 80 correspondientes a ssh y html respectivamente.

## Paso N2: Búsqueda de subdirectorios
Al entrar a la página e indagar un poco, 

raw code
```
 nmap 172.17.0.2 -sS -sVC -n -Pn -p- --open -oG open_ports
```
```
gobuster dir -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -u http://172.17.0.2/ -x php,html,txt,py
```
