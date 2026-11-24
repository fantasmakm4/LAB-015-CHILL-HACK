# RECONOCIMIENTO — LAB-015 CHILL HACK

## Objetivo

Identificar los servicios accesibles de la máquina TryHackMe Chill Hack durante la sesión observada.

## Escaneo inicial

Comando ejecutado:

```bash
sudo nmap -Pn -sV -sC -T4 -p21,22,80 10.65.175.242
```

Servicios detectados:

| Puerto | Servicio | Versión detectada |
|---|---|---|
| 21/tcp | FTP | vsftpd 3.0.5 |
| 22/tcp | SSH | OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 |
| 80/tcp | HTTP | Apache httpd 2.4.41 (Ubuntu) |

## Ampliación de puertos

Se ejecutó `sudo nmap -Pn --top-ports 1000 -T4 10.65.175.242`. El escaneo confirmó abiertos los puertos TCP 21, 22 y 80. Los otros 997 puertos del conjunto estaban cerrados.

## FTP anónimo

Nmap confirmó acceso anónimo. El directorio FTP mostró `note.txt`. Se leyó mediante curl y la nota informó del filtrado de cadenas introducidas en un comando.

## Enumeración HTTP

Gobuster se ejecutó con `/usr/share/wordlists/dirb/common.txt`. Encontró `/secret/` con redirección 301. El recurso principal respondió 200 y el título fue `Game Info`.

## Puerto local 9001

La configuración Apache inspeccionada definía un VirtualHost con `DocumentRoot /var/www/files` y puerto 9001. Desde Parrot, las solicitudes a `10.65.175.242:9001` fallaron por conexión rechazada. No se registra como puerto expuesto externamente.

## Conclusión

La superficie observada para el escaneo completo de los 1.000 puertos más comunes fue FTP, SSH y HTTP. La enumeración web localizó el formulario de comandos `/secret/`.
