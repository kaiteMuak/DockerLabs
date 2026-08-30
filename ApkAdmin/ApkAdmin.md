# ApkAdmin - Writeup

## Resumen
ApkAdmin nos presenta una maquina con el objetivo de descargar un archivo .apk para posteriormente analizarlo y buscar el usuario y la contraseña para acceder al servidor ssh y luego convertirnos en usuarios root reutilizando credenciales.
![imagen](x)

## Paso N1: Reconocimiento
Usamos la herramienta nmap para buscar puertos TCP abiertos y a la vez escanear sus versiones y servicios determinados.
```
nmap 172.17.0.2 -sS -sVC -n -Pn -p- --open --min-rate 5000 
```
![scan](x)

Podemos ver que tiene unicamente el puerto TCP 80 (http) y 22 (ssh) abiertos, por los que procederemos a buscar informacion dentro del servidor web.

## Paso N2: Visualizacion web
Al entrar a la pagina, podemos ver que nos da un boton para descargar un archivo apk.
![apk](x)

Una vez descargado, podremos analizar sus archivos internos mediante la herramienta ```jadx```

## Paso N3: Analisis de APK
Por defecto, Kali Linux no trae la herramienta jadx, por lo que procederemos a descargarla manualmente con el siguiente comando:
```
sudo apt install jadx
```
Luego de descargarla, entraremos a sus archivoos internos usando
```
jadx-gui AdminBypassCTF.apk
```
![jadx](x)
Una vez dentro de la interfaz grafica de jadx, podremos ver en la parte de la izquierda todos sus archivos internos y explorar dentro de ellos para buscar las credenciales del ssh.

Como no sabemos en cual archivo se encuentra, usaré la herrmienta de busqueda que se encuentra en el menu de arriba a la derecha y filtrare por palabras claves. En este caso, contrasena y nos muestra un resultado
![jadx](x)

Ahora, ya sabemos que el usuario es pingu y la contraseña es chocolate para acceder al servidor ssh.

## Paso N4: conexion ssh y root
Para conectarnos al ssh usamos el siguiente comando con las credenciales obtenidas
```
ssh pingu@172.17.0.2
```
Una vez dentro del usuario pingu, ejecutamos el comando ```sudo -l``` y nos devuelve ```-bash: sudo: command not found``` lo que nos dice que sudo no es ejecutables.

intentamos ejecutar ```su root``` y reutilizamos las credenciales que encontramos analizando el apk para entrar en el servidor ssh (una mala practica de seguridad comun) y se ejecuta correctamente, por lo que nos convertimos en usuarios root.
