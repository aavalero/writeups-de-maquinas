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

