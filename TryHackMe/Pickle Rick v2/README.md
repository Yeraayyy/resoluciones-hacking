# Resolución de la Máquina: RootMe

- **Plataforma:** TryHackMe
- **Dificultad:** Fácil
- **Fecha de resolución:** 15/06/2025
- **Sistema Operativo:** Linux
- **Categoría:** 


## Descripción

"Pickle Rick" es una máquina de nivel fácil alojada en TryHackMe, orientada a quienes están comenzando en el mundo del hacking ético y la resolución de CTFs. Se enfoca principalmente en vectores de ataque web, con una pequeña fase de post-explotación en entorno Linux.

Durante la resolución, el atacante debe identificar una vulnerabilidad en una aplicación web que permite la ejecución remota de comandos (RCE), facilitando el acceso inicial al sistema como usuario web (www-data). Luego, se debe realizar una leve fase de enumeración para encontrar credenciales internas y completar los objetivos propuestos.

Esta máquina es especialmente útil para:

> Practicar la enumeración web a través de código fuente y funciones personalizadas en scripts PHP

> Identificar y explotar puntos de inyección de comandos

> Utilizar credenciales internas para acceder a información protegida

> Reforzar conceptos básicos de manipulación de archivos en sistemas Linux desde entornos restringidos


## Enumeración

### Escaneo con Nmap
