# Evidencias públicas — LAB-015 CHILL HACK

Resultados observados durante la sesión:

- Nmap identificó FTP, SSH y HTTP con sus banners y versiones en `02-Reconocimiento/RECONOCIMIENTO.md`.
- FTP anónimo permitió leer una nota.
- Gobuster encontró `/secret/`.
- El formulario ejecutó comandos como `www-data`.
- La regla sudo permitió ejecutar el script de ayuda como `apaar`.
- La cuenta `anurodh` pertenecía al grupo Docker.
- Se confirmó acceso `root` mediante Docker y `chroot`.
- Se recuperaron ambas banderas. No se publican sus valores.

No se incluyen contraseñas, hashes, llaves ni respuestas del cuestionario.
