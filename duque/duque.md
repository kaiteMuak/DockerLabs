# duque - Writeup

## Resumen
lorem ipsum



raw code
```
 nmap 172.17.0.2 -sS -sVC -n -Pn -p- --open -oG open_ports
```
```
gobuster dir -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -u http://172.17.0.2/ -x php,html,txt,py
```
