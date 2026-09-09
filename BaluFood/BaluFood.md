# BaluFood - Writeup

## Resumen 

## Paso N1: Reconocimiento 
Empezamos escaneando los puertos tcp de la máquina con sus respectivas versiones y servicios:
```
172.17.0.2
```
![]()

Vemos que estan abiertos los puerto 5000 y 22 correspondiente a servicios http y ssh respectivamente.

## Paso N2: Visualización de la página
Entramos a la página con el siguiente enlace ```http://172.17.0.2/5000```, le especificamos que queremos entrar en el puerto 5000, pues ahí es donde está la página web, de no especificar el puerto, la página no cargaria.

Una vez investigada la pagina, llegariamos a la página de login ```http://172.17.0.2:5000/login```.
Cómo no tenemos información de que credenciales usar, podremos usar credenciales básicas como lo seria ```admin:admin```
y lograremos entrar
![]()

## Paso N3: Obtención de credenciales ssh y pivoting
Si vemos el código fuente de la página, veremos las credenciales del ssh
