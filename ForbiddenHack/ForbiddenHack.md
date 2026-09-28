raw 
```
nmap 172.17.0.2 -sS -sVC -n -Pn -p- --open --min-rate 5000 -oG open_ports
 wfuzz -c -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt --hl 38 -u http://bypass403.pw/index.php?FUZZ=test -H "Referer: http://bypass403.pw"


```
