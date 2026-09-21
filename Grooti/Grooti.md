# Grooti - Writeup

## Resumen
Lorem ipsum

## Paso N1: Reconocimiento
Empezamos escaneando los puertos TCP abiertos en la máquina con sus respectivos servicios y versiones:
```
nmap 172.17.0.2 -sS -sVC -n -Pn -p- --open --min-rate 5000 -oG open_ports
```
<img width="669" height="569" alt="image" src="https://github.com/user-attachments/assets/ec2d2dac-9a1f-4510-ac89-9fe6f7630bb7" />

Vemos que están abiertos los puertos 22, 80 y 3306 correspondientes respectivamente a ssh, http y mysql

## Paso N2: Buscando subdirectorios
Explorando un poco por el apartado visual, llegaremos a ```http://172.17.0.2/imagenes/README.txt``` el cual nos dice que hay una contraseña: ```password1``` (sin dar mas credenciales), por lo que pasaremos a buscar subdirectorios.
```
gobuster dir -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -u http://172.17.0.2/ -x php,html
```
<img width="667" height="438" alt="image" src="https://github.com/user-attachments/assets/20886c9e-bd56-40a1-8bda-cefa92ca3e5a" />

y encontraremos ```/secret/```. Al entrar, mostrará un botón para descargar un archivo, cual contenido es ```mysql -u rocket -p -h 172.17.0.2 --ssl=0```

## Paso N3: Entrando al intérprete de mariaDB
Con el comando conseguido en el paso anterior, entraremos al interprete de mariaDB usando la contraseña encontrada ```password1```
```
mysql -u rocket -p -h 172.17.0.2 --ssl=0
```
una vez dentro del intérprete, veremos que hay una base de datos llamada ```files_secret```, dentro tenemos la tabla ```rutas``` la cual si vemos su contenido, vemos que hay una fila de nombre ```secret``` y de ruta ```/unprivate/secret``` 
```
SHOW DATABASES;
USE files_secret
SHOW TABLES;
SELECT * FROM rutas;
```
<img width="446" height="171" alt="image" src="https://github.com/user-attachments/assets/62afe944-09f2-4ead-b005-cd94fd813dc6" />
Por lo que ahora entraremos al navegador con esa ruta ```http://172.17.0.2/unprivate/secret/```
