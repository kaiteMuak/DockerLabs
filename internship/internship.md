# internship - Writeup

## Resumen 
lorem ipsum

## Paso N1: Reconocimiento 
Empezamos escaneando los puertos TCP de la máquina con el siguiente comando:
```
nmap 172.17.0.2 -sS -sVC -n -Pn -p- --open --min-rate 5000
```
![scan](x)
Podemos ver que están los puertos 22 y 80 abiertos, no tenemos las credenciales correspondientes al ssh, por lo que tocará investigar el servidor web.

## Paso N2: Investigación
Al entrar a la página, no encontraremos información relevante, por lo que haremos un ```curl``` (o en su defecto inspeccionaremos la página) y encontraremos una línea interesante:
```
<link rel="dns-prefetch" href="//gatekeeperhr.com" />
```
![]()

Por lo que ahora, configuraremos ```/etc/hosts``` para acceder a la página, de la siguiente forma:
```
172.17.0.2 gatekeeperhr.com
```

## Paso N3: SQL Injection
Ahora sí, una vez dentro de la página, podremos acceder a una pestaña de loggin, en la cuál pide un usuario y una contraseña, por lo que intentamos hacer una SQLi de la siguiente manera:
![]()

```' OR 1=1 -- -``` funciona de la siguiente manera: ```'``` sirve para cerrar la cadena de texto donde se encuentra el input, ```OR 1=1``` se usa como un condicional, para que devuelva TRUE, haciendo que la condicion se cumpla sin importar los datos reales, ```-- -``` los primero dos guíones sirven para comentar el resto de la línea, el espacio y guíon restante sirve de relleno para asegurar que la inyección se ejecute correctamente, por lo que ahora tenemos acceso a la página.

## Paso N4: Accediendo a usuarios
Al acceder a la página, tenemos una lista con las columnas: id, nombre, departamento y fecha de inicio. No tenemos alguna idea de que hacer con eso, por lo que buscamos diferentes directorios para buscar mas información con gobuster
```
 gobuster dir -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt -u http://gatekeeperhr.com/
```
![]()

Y encontramos el subdirectorio /spam, que al hacerle un ```curl``` (o en su defecto entrar e inspeccionar la página) encontraremos un comentario:
```
<!-- Yn pbagenfrñn qr hab qr ybf cnfnagrf rf 'checy3' -->
```
Por intuición, supe que era un cifrado ROT13, por lo que procedo a descifrarlo de manero online y queda el siguiente texto
```
<!-- La contraseña de uno de los pasantes es 'purpl3' -->
```
