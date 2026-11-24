# EXPLOTACIÓN Y ESCALADA — LAB-015 CHILL HACK

## Ejecución de comandos web

`/secret/` recibió el parámetro POST `command`. La ejecución de `id`, `pwd` y `whoami` confirmó comandos ejecutados como `www-data`. El código PHP comparaba tokens con una lista de bloqueo que incluía `ls`, `cat`, `head`, entre otros. El control no impedía la ejecución de comandos alternativos.

## Paso a apaar

`sudo -l` como `www-data` permitió ejecutar `/home/apaar/.helpline.sh` como `apaar` sin contraseña. El script ejecutaba directamente el contenido de la variable del mensaje. Se usó una tubería `printf` para proporcionar entradas al script y ejecutar comandos como `apaar`. Se localizó y leyó `/home/apaar/local.txt`. El contenido de la bandera no se publica.

## Acceso SSH como anurodh

Se verificó el acceso SSH como `anurodh`. La contraseña se omite. El comando `id` confirmó que la cuenta pertenecía al grupo `docker`.

## Escalada a root

El acceso al daemon Docker permitió montar el sistema raíz del anfitrión dentro de un contenedor Alpine y ejecutar `chroot`:

```bash
docker run -v /:/mnt --rm -it alpine chroot /mnt /bin/sh
```

Dentro del contenedor, `whoami` devolvió `root`. Se leyó `/root/proof.txt`. El valor de la Root Flag no se publica.

## Vulnerabilidades verificadas

- Ejecución de comandos del sistema desde un parámetro web.
- Lista de bloqueo de tokens como control insuficiente.
- Ejecución de entrada controlada por el usuario en un script accesible mediante sudo.
- Cuenta ordinaria dentro del grupo Docker con capacidad de escalar a root.
- Credenciales de base de datos almacenadas en el código fuente. No se reproducen.

No se confirmó una CVE aplicable a estos hallazgos.

## Mitigaciones

1. Retirar `shell_exec` de entradas web y usar operaciones permitidas explícitamente.
2. No ejecutar variables de usuario como comandos de shell.
3. Restringir sudoers y auditar scripts ejecutados con privilegios.
4. Retirar cuentas ordinarias del grupo Docker y proteger el socket del daemon.
5. Sacar secretos del código fuente y rotar credenciales expuestas.
6. Limitar servicios locales a las interfaces y redes estrictamente necesarias.

## Resultado

Acceso `root` confirmado. Las banderas User y Root fueron recuperadas. No se verificó que TryHackMe aceptara las respuestas. Sus valores no se incluyen.
