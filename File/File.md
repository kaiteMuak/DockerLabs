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

Vemos que tenemos los subdirectorios ```/uploads/``` y ```file_upload.php``` disponibles.



raw
```
gobuster dir -u http://172.17.0.2 -w /usr/share/wordlists/seclists/Discovery/Web-Content/DirBuster-2007_directory-list-2.3-medium.txt -x txt,php,html


```
