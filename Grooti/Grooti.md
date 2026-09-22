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

## Paso N4: Interceptando peticiones con burpsuite
<img width="566" height="476" alt="image" src="https://github.com/user-attachments/assets/2e2867f1-c0b1-4122-99c1-e2e56a68ca40" />

Dentro de la página, veremos un formulario el cual pide un texto y un número del 1 al 100, si ponemos un número equivocado, nos descargará un ```.txt``` sin relevancia, por lo que tendremos que buscar el número correcto y para facilitar el trabajo usaremos burpsuite.

<img width="536" height="269" alt="image" src="https://github.com/user-attachments/assets/cda06d35-a237-44ee-a4a1-e2d1e05fd438" />

Una vez interceptada la petición con burpsuite, mandaremos la información al ```intruder``` desde el ```http history```.
En la ultima línea ```content=test&number=1``` subrayaremos el '1' y le daremos al boton ```Add $```, quedando así ```content=test&number=§1§```.

Luego, configuraremos el payload en tipo ```Number``` con con rango de numeros secuencial, del 1 al 100, 1 por 1, las configuraciones se verán visualmente en la siguiente imagen.
<img width="1333" height="475" alt="image" src="https://github.com/user-attachments/assets/5d07afea-4aee-4eee-aa54-8f61a89782bd" />

Luego de configurar todo, empezaremos el ataque. Una vez terminado, veremos que el número 16 tiene mas caracteres que los demas, por lo que ahora, volveremos a la página y descargaremos el archivo correspondiente al numero 16, el cual es ```password16.zip```

## Paso N5: Fuerza bruta con john
Ya que tenemos un ```.zip``` que al descomprimirlo pide contraseña, le aplicaremos fuerza bruta.
Como ```john``` no puede leer archivos comprimidos, usaremos ```zip2john``` para pasarlo a un formato legible por john
```
zip2john password16.zip > hash.txt
```
Luego, usamos una john para hacerle fuerza bruta a la contraseña 
```
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
```
Y luego visualizamos la contraseña de ```password16.zip```
```
john --show hash.txt
```
y vemos que la contraseña es ```password1```
<img width="668" height="384" alt="image" src="https://github.com/user-attachments/assets/2f2fac69-8b5c-4283-88a1-b4e338bbc042" />

Una vez descomprimido, nos dará ```password16.txt``` el cual parece ser una wordlist

## Paso N6: Accediendo a usuario Grooti
Aplicaremos fuerza bruta al usuario grooti con la wordlist obtenida anteriormente ```password16.txt```
```
hydra -l grooti -P password16.txt ssh://172.17.0.2
```
<img width="670" height="385" alt="image" src="https://github.com/user-attachments/assets/ef41aebe-7fc0-48c0-9617-7edfa23ed0c8" />

Vemos que la contraseña para el usuario ```grooti``` es ```YoSoYgRoOt```, por lo que ahora con la credenciales obtenidas podemos entrar al servidor ssh ```ssh grooti@172.17.0.2```

## Paso N7: Escalando privilegios privilegios
Viendo la programación de crontab con ```crontab -l 2>/dev/null``` vemos que se está ejecutando un script en /opt/cleanup.sh
