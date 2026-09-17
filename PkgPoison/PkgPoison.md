# PkgPoison - Writeup

## Resumen
Lorem ipsum

## Paso N1: Reconocimiento 
Empezamos escaneando los puertos TCP con sus respectivos servicios y versiones
```
nmap 172.17.0.2 -sS -sVC -n -Pn -p- --open --min-rate 5000 -oG open_ports
```
y vemos que están los puertos 22 y 80 correspondientes a ssh y http respectivamente. 
<img width="668" height="438" alt="image" src="https://github.com/user-attachments/assets/abe66963-21fe-444f-9163-b8b7a0c1bd50" />

## Paso N2: Descubrimiento de subdirectorios
En la página inicial no encontraremos gran cosa, por lo que empezamos la busqueda de subdirectorios en busca de información.
```
gobuster dir -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -u http://172.17.0.2/ -x php,html
```
<img width="667" height="482" alt="image" src="https://github.com/user-attachments/assets/1f81b6bc-2918-4829-9b73-54d21348960f" />

encontraremos el subdirectorio ```/notes/``` el cual nos lleva a note.txt y contiene contenido interesante, nos dice que las credenciales antiguas son ```dev:developer123```, por lo que ya tenemos el usuario.

## Paso N3: Acceso a usuario dev
Como ya tenemos el usuario, haremos fuerza bruta para encontrar la contraseña de esta forma:
```
hydra -l dev -P /usr/share/wordlists/rockyou.txt ssh://172.17.0.2/
```
<img width="665" height="399" alt="image" src="https://github.com/user-attachments/assets/9d87cd3e-0379-46d9-a5f9-ea44918271f8" />

Encontramos que las credenciales correctas son ```dev:computer``` por lo que ahora logramos entrar al servidor ssh con las respectivas credenciales ```ssh dev@172.17.0.2```.

## Paso N4: Accediendo a usuario admin 
En el directorio home, vemos que hay un subdirectorio llamado ```admin``` lo que nos dice que hay un usuario con dicho nombre. 
Explorando un poco el sistema, veremos que en ```/opt/scripts/__pycache__``` hay un archivo llamado ```secret.cpython-38.pyc```, el cual parece tener contenido pero está compilado, por lo que usamos ```strings``` para pasarlo a un formato humanamente legible.
```
strings secret.cpython-38.pyc
```
<img width="600" height="214" alt="image" src="https://github.com/user-attachments/assets/be7b1b3b-dc86-4127-8d79-02965c24dfcb" />

y obtenemos las credenciales ```admin:p@$$w0r8321```, por lo que ahora podemos acceder a usuario ```admin```
