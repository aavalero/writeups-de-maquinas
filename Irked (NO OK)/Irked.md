### NMAP
```
nmap -p- -Pn -sS -sV 10.129.77.104
```
![](Pasted%20image%2020260119020352-1.png)
PORT      STATE SERVICE VERSION
22/tcp    open  ssh     OpenSSH 6.7p1 Debian 5+deb8u4 (protocol 2.0)
80/tcp    open  http    Apache httpd 2.4.10 ((Debian))
111/tcp   open  rpcbind 2-4 (RPC #100000)
6697/tcp  open  irc     UnrealIRCd
8067/tcp  open  irc     UnrealIRCd
42424/tcp open  status  1 (RPC #100024)
65534/tcp open  irc     UnrealIRCd

