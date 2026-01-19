### RECONOCIMIENTO

```
nmap -p- -Pn -sS -sV 10.129.76.58 
```
PORT     STATE SERVICE VERSION
80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu))
2222/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.2 (Ubuntu Linux; protocol 2.0)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```
http://10.129.76.58/
```
![[Pasted image 20260117222554.png]]
```
gobuster dir -u http://10.129.76.58 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt 
```
/server-status        (Status: 403) [Size: 300]
![[Pasted image 20260117223339.png]]

No conseguimos información relevante utilizando esta librería por que lo probamos nuevamente con una diferente
```
gobuster dir -u http://10.129.76.58 -w /usr/share/wordlists/dirb/common.txt 
```
/.hta                 (Status: 403) [Size: 291]
/.htaccess            (Status: 403) [Size: 296]
/.htpasswd            (Status: 403) [Size: 296]
/cgi-bin/             (Status: 403) [Size: 295]
/index.html           (Status: 200) [Size: 137]
/server-status        (Status: 403) [Size: 300]
```
La ruta /cgi-bin/ se utiliza para almacenar scripts de un servidor web 
```
Buscamos scripts en la ruta http://10.129.76.58/cgi-bin/
```
gobuster dir -u http://10.129.76.58/cgi-bin/ -w /usr/share/seclists/Discovery/Web-Content/common.txt -x sh
```
/user.sh              (Status: 200) [Size: 118]

Accedemos a http://10.129.76.58/cgi-bin/user.sh
![[Pasted image 20260118005953.png]]
