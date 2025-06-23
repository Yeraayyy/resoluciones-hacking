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
