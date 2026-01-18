### NMAP
```
nmap -p- -Pn -sS -sV 10.129.74.0
```
![](Pasted%20image%2020260115130710.png)

PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))

### WEB
```
http://10.129.74.0/
```
![](Pasted%20image%2020260115130710.png)
![](Pasted%20image%20260115130909.png)
![](Pasted%20image%2020260115130909.png)



### GOBUSTER
```
gobuster dir -u http://10.129.74.0 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt 
```
![](Pasted%20image%20260115133815.png)

### FEROXBUSTER
```
feroxbuster -u http://10.129.74.0/ -f -n 
```
Se observa que se tiene acceso a la ruta http://10.129.74.0/dev/ donde aparecen dos archivos relacionados con phpbash
![](Pasted%20image%20260115155913.png)

### WEB

Accedemos a la ruta http://10.129.74.0/dev/phpbash.php y observamos que hay una shell con la que podemos interactuar
![](Pasted%20image%20260115160040.png)
