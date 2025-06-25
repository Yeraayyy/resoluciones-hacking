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
