# Resolución de la Máquina: Pickle Rick

- **Plataforma:** TryHackMe
- **Dificultad:** Fácil
- **Fecha de resolución:** 15/06/2025
- **Sistema Operativo:** Linux
- **Categoría:** 


## Descripción

"Pickle Rick" es una máquina de nivel fácil alojada en TryHackMe, orientada a quienes están comenzando en el mundo del hacking ético y la resolución de CTFs. Se enfoca principalmente en vectores de ataque web, con una pequeña fase de post-explotación en entorno Linux.

Durante la resolución, el atacante debe identificar una vulnerabilidad en una aplicación web que permite la ejecución remota de comandos (RCE), facilitando el acceso inicial al sistema como usuario web (www-data). Luego, se debe realizar una leve fase de enumeración para encontrar credenciales internas y completar los objetivos propuestos.

Esta máquina es especialmente útil para:

> Practicar la enumeración web a través de código fuente y funciones personalizadas en scripts PHP

> Identificar y explotar puntos de inyección de comandos

> Utilizar credenciales internas para acceder a información protegida

> Reforzar conceptos básicos de manipulación de archivos en sistemas Linux desde entornos restringidos


## Enumeración

### Escaneo con Nmap

```bash
nmap --open -sS -T5 -sV -n -Pn 10.10.235.25
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-06-15 18:15 CEST
Stats: 0:00:06 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Nmap scan report for 10.10.235.25
Host is up (0.044s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```
### Escaneo con Gobuster

```bash
gobuster dir -u http://10.10.235.25 -w /usr/share/wordlists/wfuzz/general/common.txt  -x php,txt,html
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.235.25
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/wfuzz/general/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              php,txt,html
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/robots.txt           (Status: 200) [Size: 17]
/assets               (Status: 301) [Size: 313] [--> http://10.10.235.25/assets/]
/index.html           (Status: 200) [Size: 1062]
/login.php            (Status: 200) [Size: 882]
/portal.php           (Status: 302) [Size: 0] [--> /login.php]
Progress: 3808 / 3812 (99.90%)
===============================================================
Finished
===============================================================
```

## Intrusión
Al inspeccionar el código fuente de la página encontraremos el siguiente usuario: R1ckRul3s
Por otro lado, en robots.txt encontraremos lo que parece una contraseña: Wubbalubbadubdub

Si probamos estas credenciales en el panel de login podremos acceder.

Veremos que podemos ejecutar comandos, el primer flag lo tendremos a simple vista solo haciendo un ls, pero no podremos leerlo con cat, deberemos hacerlo con less

El segundo flag tras navegar un poco veremos que esta en "/home/rick/second ingredient"

Si ejecutamos un `sudo -l` veremos que tenemos permisos de ejecutar como sudo, navegando por los directorios hemos encontrado /root, por lo que procederemos a listarlo como sudo, ahí encontraremos el último flag.
