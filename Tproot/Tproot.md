# Tproot - Writeup

## Resumen
Lorem Ipsum

## Paso N1: Reconocimiento
Empezaremos escaneando los puertos TCP abiertos de la maquina victima con sus respectivos servicios y versiones
```
nmap 172.17.0.2 -sS -sVC -n -Pn -p- --open --min-rate 5000 -oG open_ports
```
<img width="669" height="373" alt="image" src="https://github.com/user-attachments/assets/2791bbe8-4cfb-46e9-9a64-575f622aa6a4" />

Podemos ver en pantalla que están abiertos los puertos 21 y 80 correspondientes a ftp y http respectivamente. Podemos ver que el servidor ftp está usando ```vsftpd 2.3.4```, una versión un poco antigua que tiene cierta vulnerabilidad.

## Paso N2: Intrusion medianto vsftpd 2.3.4
Investigando un poco, podemos concluir que para explotar el `vsftpd 2.3.4` tenemos que poner una carita feliz `:)` en el nombre, una vez ingresada las credenciales, a pesar de ser incorrectas se abrirá el puerto 6200. Para esta máquina usaré el usuario `kaite:)`
<img width="354" height="138" alt="image" src="https://github.com/user-attachments/assets/4b8faa6c-b669-4b1f-b2d2-8249efa1e835" />

Sin salirnos, entramos al puerto 6200 desde otra terminal con `nc 172.17.0.2 6200` y habremos entrado al sistema como usuarios root.

<img width="363" height="113" alt="image" src="https://github.com/user-attachments/assets/957dd17e-3cc8-480a-9d91-8291e47b2b17" />



## Paso N3: 

