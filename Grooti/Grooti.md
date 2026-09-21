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




raw
```
nmap 172.17.0.2 -sS -sVC -n -Pn -p- --open --min-rate 5000 -oG open_ports
gobuster dir -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -u http://172.17.0.2/ -x php,html
mysql -u rocket -p -h 172.17.0.2 --ssl=0


--- SQL ---
SHOW DATABASES;
USE files_secret
SHOW TABLES;
SELECT * FROM rutas;


```
