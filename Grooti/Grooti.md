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
