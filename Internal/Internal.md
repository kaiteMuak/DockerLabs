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

## Paso N2: Accediendo a la página (opcional)
Al acceder a ```172.17.0.2``` vemos que la página no responde, por lo que tenemos que resolver la ip manualmente agregando el dominio a ```/etc/hosts``` de esta forma: ```172.17.0.2 internal.dl```
