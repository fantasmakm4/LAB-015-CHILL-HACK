# LAB-015 — Chill Hack

![Diagrama de ataque](04-Imagenes/LAB-015-CHILL-HACK-Diagrama-Ataque.png)

| Campo | Detalle |
|---|---|
| Plataforma | TryHackMe |
| Dificultad | Fácil |
| Sistema | Ubuntu 20.04 |
| IP de laboratorio | 10.65.175.242 (sesión de práctica, puede cambiar) |
| Estado | Completado |

## Objetivo

Obtener acceso inicial, capturar las banderas de usuario y root, y documentar las vulnerabilidades verificadas en Chill Hack.

## Cadena de ataque

FTP anónimo y nota → enumeración web → ejecución remota de comandos como `www-data` → abuso del script `helpline.sh` para ejecutar como `apaar` → credencial SSH de `anurodh` → grupo Docker → shell root.

## Herramientas

Nmap, curl, Gobuster, SSH y Docker.

## Documentación

- [Informe de pentesting](01-Informes/Informe-Pentesting.md)
- [Sala TryHackMe](https://tryhackme.com/room/chillhack)

## Resultado

Se obtuvo acceso `root` mediante pertenencia de `anurodh` al grupo `docker`. Las banderas User y Root se recuperaron durante la sesión.

## Autor

FANTASMA — fantasmakm4
