# Resolución de la Máquina: RootMe
```
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
