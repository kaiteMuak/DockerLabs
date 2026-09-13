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
Al entrar a la página e indagar un poco, no encontraremos gran cosa, por lo que empezaremos la búsqueda de directorios ocultos con ```gobuster``` de la siguiente manera:
```
gobuster dir -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -u http://172.17.0.2/ -x php,html
```
![]()

y encontramos el subdirectorio ```/bills```, el cuál es una página de login.

## Paso N3: Accediendo sin credenciales
Inyectamos código sql en el nombre, poniendo ```admin' -- -``` para acceder a la página como administradores
![]()


