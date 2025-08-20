# Linux – Network Enumeration

Tras comprometer un sistema Linux, la enumeración de red permite entender la topología, conectividad y posibles objetivos internos.

---

## 1. Información de red

### Interfaces de red
```bash
ifconfig
# o en sistemas modernos:
ip a s
```
- Muestra interfaces, direcciones IP, máscaras y estado (UP/DOWN).
- Sirve para identificar en qué subred se encuentra el host.

---

## 2. Conexiones y puertos abiertos
```bash
netstat -tulpn
# o
ss -tulpn
```
- Lista conexiones activas y servicios escuchando en puertos TCP/UDP.
- `-t` TCP, `-u` UDP, `-l` listening, `-p` procesos, `-n` no resuelve nombres.

---

## 3. Tabla de enrutamiento
```bash
route -n
# o
ip route
```
- Muestra rutas conocidas por el host.
- Permite descubrir pasarelas (gateways) y otras subredes alcanzables.

---

## 4. Configuración de redes
```bash
cat /etc/networks
```
- Tabla de redes definidas por nombre en el sistema.

```bash
cat /etc/hostname
```
- Nombre del host, usado dentro de la red.

```bash
cat /etc/resolv.conf
```
- Configuración de DNS.  
- `nameserver` indica los servidores DNS usados.  
- Puede revelar servidores internos.

---

## 5. ARP – Tabla de vecinos
```bash
arp -a
```
- Muestra dispositivos en la misma red que han sido vistos por el host.
- Útil para identificar otros equipos vivos en la LAN (pivoting lateral).


