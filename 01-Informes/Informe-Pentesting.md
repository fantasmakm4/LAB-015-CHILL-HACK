# Informe de pentesting — LAB-015 Chill Hack

## Identificación

- Plataforma: TryHackMe
- Máquina: Chill Hack
- Dificultad: Fácil
- Sistema operativo observado: Ubuntu 20.04.6 LTS
- IP de esta sesión: `10.65.175.242`, asignación dinámica
- Estado: acceso root confirmado y ambas banderas recuperadas
- Cuestionario TryHackMe: no se verificó la aceptación de las respuestas
- CVE: no se verificó una CVE aplicable

## Objetivo

Reconocer la máquina, identificar vulnerabilidades explotables, obtener acceso inicial, escalar privilegios y recuperar las banderas de usuario y root. Los valores de las banderas y las contraseñas se omiten.

## Reconocimiento

Escaneo inicial de los puertos identificados:

```bash
sudo nmap -Pn -sV -sC -T4 -p21,22,80 10.65.175.242
```

Resultado observado:

```text
21/tcp open ftp vsftpd 3.0.5
22/tcp open ssh OpenSSH 8.2p1 Ubuntu 4ubuntu0.13
80/tcp open http Apache httpd 2.4.41 (Ubuntu)
```

Se amplió el reconocimiento a los 1.000 puertos TCP más comunes:

```bash
sudo nmap -Pn --top-ports 1000 -T4 10.65.175.242
```

Nmap volvió a mostrar abiertos 21, 22 y 80.

## Enumeración

FTP permitió sesión anónima. El directorio mostraba `note.txt`, que se descargó y leyó con:

```bash
curl -sS --user anonymous:anonymous ftp://10.65.175.242/note.txt
```

La nota mencionaba filtrado de cadenas introducidas en un comando. La enumeración web se realizó con:

```bash
gobuster dir -u http://10.65.175.242 -w /usr/share/wordlists/dirb/common.txt
```

Gobuster encontró `/secret/`. La ruta devolvió un formulario POST con el campo `command`. También aparecieron rutas de recursos estáticos y respuestas 404 para enlaces de la plantilla que no correspondían a páginas existentes.

## Explotación web y acceso como www-data

Se comprobó ejecución de comandos del sistema en el parámetro POST:

```bash
curl -sS -X POST -d 'command=id' http://10.65.175.242/secret/
```

La salida identificó `www-data`. También se usaron `pwd` y `whoami`. La inspección del código PHP mostró una lista de bloqueo por coincidencia exacta de tokens. `ls` y `head` provocaron la respuesta de bloqueo, mientras `find`, `grep` y `sed` se ejecutaron. Este filtro no evita la ejecución arbitraria de comandos.

La búsqueda de archivos encontró `/home/apaar/local.txt` y `/home/apaar/.helpline.sh`. `sudo -l` indicó que `www-data` podía ejecutar el script como `apaar` sin contraseña.

## Escalada a apaar

El script `helpline.sh` solicita un nombre y un mensaje, después ejecuta el mensaje como un comando:

```bash
$msg 2>/dev/null
```

Se utilizó entrada canalizada al script bajo `sudo -u apaar`, y se leyó la bandera de usuario de `/home/apaar/local.txt`. No se publica su valor.

## Acceso como anurodh y escalada a root

Se inició sesión por SSH como `anurodh`. `id` confirmó pertenencia al grupo `docker`. El abuso de ese grupo permitió montar el sistema raíz del anfitrión desde un contenedor:

```bash
docker run -v /:/mnt --rm -it alpine chroot /mnt /bin/sh
```

Dentro del entorno, `whoami` devolvió `root`. Se leyó `/root/proof.txt` y se recuperó la bandera root. Su valor no se publica.

## Servicios y versiones

| Puerto | Servicio | Versión observada |
|---|---|---|
| 21/tcp | FTP | vsftpd 3.0.5 |
| 22/tcp | SSH | OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 |
| 80/tcp | HTTP | Apache 2.4.41 Ubuntu |

La configuración inspeccionada de Apache declaraba un VirtualHost local en el puerto 9001. Las solicitudes remotas a ese puerto no conectaron. No se incluye como puerto externo abierto.

## Vulnerabilidades verificadas

- **Ejecución remota de comandos web.** El campo `command` llegaba a `shell_exec` con controles de lista de bloqueo insuficientes.
- **Ejecución insegura con sudo.** `www-data` podía ejecutar `helpline.sh` como `apaar`, y el script ejecutaba directamente entrada controlada por el usuario.
- **Abuso de Docker.** La cuenta `anurodh` pertenecía al grupo `docker`, con capacidad de montar el sistema del anfitrión y obtener privilegios root.
- **Secretos embebidos.** El código de la aplicación contenía credenciales de base de datos. No se reproducen aquí.

No se asigna CVE. No se confirmó una CVE aplicable a estos hallazgos.

## Mitigaciones

- Eliminar `shell_exec` de rutas controladas por usuarios. Preferir funciones de aplicación específicas, validación por lista permitida y separación de argumentos.
- No ejecutar contenido de variables como órdenes shell. Aplicar controles estrictos de entrada y revisión del flujo de privilegios.
- Limitar sudoers al mínimo de comandos y argumentos, y auditar scripts llamados con privilegios elevados.
- Retirar usuarios ordinarios del grupo Docker y proteger el socket del daemon.
- Sacar secretos del código, almacenarlos en un gestor adecuado, limitar permisos y rotar cualquier credencial expuesta.
- Exponer servicios solo en las interfaces y redes requeridas.

## Conclusión

La cadena de ataque permitió pasar de acceso FTP anónimo a ejecución como `www-data`, después a `apaar`, luego a `anurodh` y finalmente a root mediante Docker. Se recuperaron ambas banderas. No se incluyen contraseñas ni contenidos de banderas, y no se afirma que las respuestas hayan sido aceptadas por TryHackMe.
