## Internal - Writeup

## Resumen
Lorem ipsum

## Paso N1: Reconocimiento
Empezamos escaneando los puertos TCP de la máquina con sus respectivos servicios y versiones
```
nmap 172.17.0.2 -sS -sVC -n -Pn -p- --open -oG open_ports
```
![]

Vemos que están los puertos 22 y 80 correspondientes a ssh y http.

## Paso N1.2: Accediendo a la página (opcional)
Al acceder a ```172.17.0.2``` vemos que el servidor no muestra el contenido esperado, por lo que agregamos el dominio al ```/etc/hosts``` de esta forma: ```172.17.0.2 internal.dl```. 
Dentro de la página, veremos que no hay información relevante, por lo que pasaremos al siguiente paso.

## Paso N2: Busqueda de hosts
Como en la página inicial no encontramos información relevante, pasaremos a buscar hosts con gobuster de la siguiente forma:
```
gobuster vhost -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -u http://internal.dl/ --append-domain | grep 'Status: 200'
```
Usamos ```grep``` para filtrar especificamente por los dominios que devuelvan status 200, de lo contrario en este caso se mostraran todos los dominios
![]()

vemos que el ```backup.internal.dl``` devuelve status 200. 
Al entrar, veremos que el servidor no devuelve contenido nuevamente, por lo que lo agregamos a ```/etc/hosts``` ```172.17.0.2 backup.internal.dl```

## Paso N3: entendiendo el WAF
Al entrar a la página, veremos que hay una consola, la cuál solo nos permite ejecutar 5 comandos ya preestablecidos y abajo veremos una consola con el output
