# Enumeración de SNMP (Simple Network Management Protocol)

## ¿Qué es SNMP?

SNMP es un protocolo de red utilizado para monitorizar y administrar dispositivos en redes IP (routers, switches, servidores, etc.).

- Usa por defecto el puerto **UDP 161** para peticiones y **UDP 162** para traps.
- Versiones comunes:
  - **SNMPv1/v2c:** Usa una comunidad (ej. `public`, `private`) como autenticación.
  - **SNMPv3:** Usa autenticación y cifrado (más segura).

---

## Paso 1: Escaneo de puertos SNMP con Nmap

### Detectar puertos UDP abiertos

```bash
nmap -sU -p- <IP>
```

> Resultado esperado:
```
161/udp open  snmp
```

---

## Paso 2: Enumeración con Nmap NSE

### 1. Descubrimiento de usuarios con fuerza bruta

```bash
nmap -sU -p 161 --script=snmp-brute <IP>
```

> Este script intenta descubrir las cadenas de comunidad válidas (ej: `public`, `private`).

---

### 2. Descubrir información básica del sistema

```bash
nmap -sU -p 161 --script=snmp-info <IP>
```

> Devuelve el nombre del host, contact, ubicación, sistema operativo, etc.

---

### 3. Dump completo de OIDs disponibles

```bash
nmap -sU -p 161 --script=snmp-interfaces <IP>
nmap -sU -p 161 --script=snmp-netstat <IP>
nmap -sU -p 161 --script=snmp-processes <IP>
nmap -sU -p 161 --script=snmp-win32-users <IP>
```

> Scripts que devuelven interfaces, procesos en ejecución, usuarios Windows, etc.

---

### 4. Todos los scripts SNMP NSE

```bash
nmap -sU -p 161 --script "snmp-*" <IP> -oN snmp_info.txt
```

> Guarda toda la salida en `snmp_info.txt` para posterior análisis.

---

## Paso 3: Usar `snmpwalk` para enumerar manualmente

Después de identificar una comunidad válida como `public`:

```bash
snmpwalk -v 1 -c public <IP>
```

> Dump completo de los objetos SNMP disponibles.

---

## Paso 4: Ataque con Hydra usando usuarios obtenidos de SNMP

Si el script `snmp-win32-users` devuelve usuarios válidos, se puede lanzar un ataque de fuerza bruta contra SMB, RDP, FTP, etc.

### Ejemplo con SMB:

```bash
hydra -L users.txt -P /usr/share/wordlists/rockyou.txt smb://<IP>
```

### Ejemplo con RDP:

```bash
hydra -L users.txt -P /usr/share/wordlists/rockyou.txt rdp://<IP>
```

---

## Archivos útiles

- **users.txt**: archivo con nombres de usuarios obtenidos por SNMP.
- **rockyou.txt**: diccionario de contraseñas común.

---

## Notas

- SNMPv1 y v2c son inseguros, no cifran tráfico ni autenticación.
- Asegúrate de tener permisos para realizar estos escaneos en entornos reales.
- Algunos dispositivos pueden tener habilitado SNMP con la cadena `public` por defecto.

---

## Referencias

- https://nmap.org/nsedoc/scripts/snmp-brute.html
- https://wiki.wireshark.org/SNMP
- https://linux.die.net/man/1/snmpwalk

