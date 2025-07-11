```bash
en samba habia politicas que se han estado analizando
```
```bash
└─# rpcclient -U levi.james%KingofAkron2025! -W puppy.htb 10.10.11.70


```rpcclient $> enumdomusers
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
```bash
└─# ntpdate puppy.htb
2025-07-11 13:06:14.736452 (-0400) +25200.486878 +/- 0.026779 puppy.htb 10.10.11.70 s1 no-leap
CLOCK: time stepped by 25200.486878
                                                                                                                                                                                                                                           
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
