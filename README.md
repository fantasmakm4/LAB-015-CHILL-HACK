![Portada oficial FANTASMA — LAB-015 Chill Hack. Infografía panorámica en paleta cian, azul oscuro y rojo. El avatar FANTASMA, una calavera encapuchada con cuernos, ojos rojos y alas de murciélago, aparece ante una luna roja con el portátil FANTASMA. Los paneles muestran TryHackMe, dificultad Fácil, Ubuntu 20.04.6 e IP de sesión; los servicios FTP 21, SSH 22 y HTTP 80 con sus versiones; la cadena de reconocimiento, acceso FTP anónimo, ejecución web como www-data, paso por apaar y anurodh y escalada a root mediante Docker. Incluye herramientas y mitigaciones. Indica que no se confirmó CVE y que se recuperaron dos banderas sin revelar sus valores.](04-Imagenes/LAB-015-CHILL-HACK.png)

# 😈 FANTASMA | LAB-015 — CHILL HACK

**TryHackMe | Fácil | Ubuntu 20.04.6 | FTP, SSH, HTTP | Ejecución remota de comandos | Escalada por sudo y Docker | Acceso root confirmado | Banderas recuperadas, valores omitidos**

## Información del laboratorio

- Plataforma: TryHackMe
- Máquina: Chill Hack
- IP observada: `10.65.175.242` (dinámica de la sesión)
- Dificultad: Fácil
- Sistema: Ubuntu 20.04.6 LTS
- Estado: acceso root confirmado
- Banderas: User y Root recuperadas, sin publicar sus valores
- Cuestionario: no se verificó la aceptación de las respuestas en TryHackMe
- CVE: no se verificó una CVE aplicable
- Autor: FANTASMA

## 1. Objetivo

Enumerar la máquina, identificar servicios y vulnerabilidades, obtener acceso inicial y escalar privilegios hasta root.

## 2. Reconocimiento

Nmap identificó FTP en 21/tcp, SSH en 22/tcp y HTTP en 80/tcp. El escaneo de los 1.000 puertos TCP más comunes confirmó esos tres puertos abiertos. FTP permitió inicio anónimo y mostró `note.txt`.

## 3. Enumeración

Gobuster encontró `/secret/`. El formulario POST de esa ruta recibía el campo `command`. La nota FTP advertía que se filtraban cadenas introducidas en un comando.

## 4. Explotación y acceso inicial

La aplicación ejecutaba comandos como `www-data`. El filtro de tokens exactos no impedía el uso de comandos alternativos. La regla sudo permitía a `www-data` ejecutar `/home/apaar/.helpline.sh` como `apaar`. El script ejecutaba directamente el mensaje recibido, lo que permitió ejecutar comandos con esa cuenta y recuperar la User Flag.

## 5. Escalada de privilegios

Se inició sesión por SSH como `anurodh`. La pertenencia al grupo `docker` permitió montar el sistema raíz del anfitrión desde un contenedor y usar `chroot` para obtener root. Se confirmó el nivel con `whoami` y se recuperó la Root Flag.

## 6. Servicios identificados

| Puerto | Servicio | Versión observada |
|---|---|---|
| 21/tcp | FTP | vsftpd 3.0.5 |
| 22/tcp | SSH | OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 |
| 80/tcp | HTTP | Apache 2.4.41 (Ubuntu) |

Apache tenía configurado un VirtualHost local en el puerto 9001. Las conexiones desde Parrot fueron rechazadas, por lo que no se considera un puerto remoto abierto.

## 7. Herramientas utilizadas

Nmap, Gobuster, curl, Firefox, SSH, Docker, `find`, `grep`, `sed`, `mysql`, `sudo`, `printf`, `id`, `pwd`, `whoami` y `wc`.

El registro de los comandos ejecutados está en [06-Comandos/COMANDOS.md](06-Comandos/COMANDOS.md).

## 8. Resultados

Acceso root confirmado y dos banderas recuperadas. No se incluyen contraseñas ni valores de las banderas. No se verificó el envío de respuestas en el cuestionario TryHackMe.

## 9. Documentación

- [Reconocimiento](02-Reconocimiento/RECONOCIMIENTO.md)
- [Explotación y escalada](03-Explotacion/EXPLOTACION-Y-ESCALADA.md)
- [Informe técnico](01-Informes/INFORME-TECNICO.md)
- [Evidencias públicas](05-Evidencias/publico/README.md)
- [Portada oficial](04-Imagenes/LAB-015-CHILL-HACK.png)
- [Sala TryHackMe](https://tryhackme.com/room/chillhack)

## 10. Conclusiones y recomendaciones

1. Eliminar la ejecución de comandos del sistema desde formularios web.
2. No ejecutar entradas de usuario como código shell.
3. Aplicar mínimo privilegio en sudoers y revisar scripts llamados con privilegios.
4. No incluir usuarios ordinarios en el grupo Docker.
5. Retirar secretos del código fuente y rotar credenciales expuestas.
6. Limitar servicios locales a las interfaces necesarias.

---
**FANTASMA | LAB-015 | CHILL HACK | ACCESO ROOT CONFIRMADO**
