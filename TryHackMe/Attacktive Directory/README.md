Resolución de la Máquina: Attacktive Directory

**-Plataforma:** TryHackMe

**-Dificultad:** Media

**-Fecha de resolución:** 18/06/2025

 **-Sistema Operativo:** Windows

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
