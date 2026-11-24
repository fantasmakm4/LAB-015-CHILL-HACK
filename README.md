![Portada oficial FANTASMA — LAB-015 Chill Hack](04-Imagenes/LAB-015-CHILL-HACK-Diagrama-Ataque.png)

# LAB-015 — CHILL HACK

| Campo | Detalle |
|---|---|
| Plataforma | TryHackMe |
| Máquina | Chill Hack |
| Dificultad | Fácil |
| Estado | Completado en la máquina. Envío de respuestas a TryHackMe no verificado. |
| Sistema operativo | Ubuntu 20.04.6 LTS |
| IP observada | `10.65.175.242` durante esta sesión, puede cambiar |
| CVE | No se verificó una CVE aplicable |
| Banderas | User y Root recuperadas. Valores omitidos |

## Descripción

Chill Hack es un laboratorio de enumeración y escalada de privilegios. La cadena observada comenzó con FTP anónimo, siguió con ejecución de comandos en el formulario `/secret/` como `www-data`, abuso del script `helpline.sh` para ejecutar como `apaar` y acceso SSH como `anurodh`. La pertenencia de `anurodh` al grupo `docker` permitió obtener una shell root.

## Servicios identificados

| Puerto | Servicio | Versión observada |
|---|---|---|
| 21/tcp | FTP | vsftpd 3.0.5 |
| 22/tcp | SSH | OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 |
| 80/tcp | HTTP | Apache 2.4.41 Ubuntu |

Apache también tenía un VirtualHost configurado para `127.0.0.1:9001` según el archivo de configuración inspeccionado. No se confirmó acceso externo a ese puerto.

## Metodología resumida

1. Reconocimiento con Nmap en los puertos observados.
2. Enumeración de FTP anónimo y lectura de `note.txt`.
3. Enumeración web con Gobuster, que encontró `/secret/`.
4. Explotación del parámetro POST `command` para ejecutar comandos como `www-data`.
5. Escalada a `apaar` mediante la regla sudo sin contraseña y la ejecución insegura de entrada en `.helpline.sh`.
6. Acceso SSH como `anurodh` y escalada a root abusando del grupo Docker.

## Herramientas y comandos usados

Nmap, Gobuster, curl, SSH, Docker, `find`, `grep`, `sed`, `sudo`, `printf`, `id` y `whoami`.

Comando de escalada final observado:

```bash
docker run -v /:/mnt --rm -it alpine chroot /mnt /bin/sh
```

## Documentación

- [Informe técnico](01-Informes/Informe-Pentesting.md)
- [Sala TryHackMe](https://tryhackme.com/room/chillhack)

## Recomendaciones

Eliminar la ejecución de comandos desde entradas web, evitar ejecutar texto controlado por usuarios en scripts shell, restringir reglas sudo, retirar cuentas ordinarias del grupo Docker y guardar secretos fuera del código fuente.

## Autor

FANTASMA — [fantasmakm4](https://github.com/fantasmakm4)
