# PingCTF - Writeup

## Resumen
Esta maquina

## Paso N1: Reconocimiento
Usamos nmap para el reconociminento basico de puertos TCP para escanear sus determinados servicios y versiones
```
nmap 172.17.0.2 -sS -sVC -n -Pn -p- --open --min-rate 5000 
```
![TCP scan](images/img1.png)
Esta abierto unicamente el puerto 80, por lo que habra que investigar la pagina para buscar una vulnerabilidad.

## Paso N2: Visualizacion web e inyeccion 
Al entrar, podemos ver que la web se compone unicamente de un buscador, por lo que intentamos una inyeccion a nivel de sistema operativo (OS Command Injection) anteponiendo ";" del comando (usamos el ; porque actúa como un separador de comandos en sistemas operativos tipo Unix/Linux). 
![Shell injection](images/img2.png)
