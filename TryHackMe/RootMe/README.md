# Resolución de la Máquina: RootMe

# 🧰 Resolución: RootMe

- **Plataforma:** TryHackMe
- **Dificultad:** Fácil
- **Fecha de resolución:** 14/06/2025
- **IP:** 10.10.71.90
- **Sistema Operativo:** Linux
- **Categoría:** Escalada de privilegios

---

## 📝 Descripción

"Root Me" es una máquina de nivel fácil diseñada para introducir a los usuarios en conceptos fundamentales de pruebas de penetración web y escalada de privilegios en sistemas Linux. Es ideal para principiantes que están dando sus primeros pasos en entornos de CTF.

Durante la resolución, el atacante debe identificar una vulnerabilidad web común que permite obtener acceso inicial al sistema. Posteriormente, se explora una sencilla pero efectiva técnica de escalada de privilegios basada en archivos SUID mal configurados.

Esta máquina es especialmente útil para:

> Practicar enumeración web y de servicios básicos

> Comprender cómo aprovechar inyecciones simples o ejecución remota de comandos

> Aplicar técnicas básicas de post-explotación

> Identificar binarios con permisos indebidos en sistemas Linux

---

## 🔍 Enumeración

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

