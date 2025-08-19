# Post-Explotación en Windows: Información de Red

Tras ganar acceso a una máquina Windows con **Meterpreter**, podemos recopilar información clave de la red para identificar nuevas rutas, posibles pivotings o configuraciones de firewall.

---

## 1. Ver configuración de red
```bash
meterpreter > shell
C:\> ipconfig /all
```
- Muestra interfaces de red.
- IP asignadas, máscaras, puertas de enlace.
- DNS configurados.
- Sirve para identificar otras subredes accesibles.

---

## 2. Ver tabla de rutas
```bash
C:\> route print
```
- Muestra todas las rutas activas.
- Permite ver qué redes puede alcanzar la máquina.
- Útil para decidir si hacer **pivoting** o crear rutas en Metasploit con `autoroute`.

---

## 3. Ver tabla ARP (equipos vecinos conocidos)
```bash
C:\> arp -a
```
- Lista IPs y MACs detectadas en la red local.
- Sirve para descubrir máquinas cercanas sin necesidad de escanear con nmap.
- Útil para encontrar objetivos que no son visibles directamente desde el atacante.

---

## 4. Ver reglas del firewall
```bash
C:\> netsh advfirewall firewall dump
```
- Extrae reglas actuales del firewall de Windows.
- Indica qué puertos están bloqueados/permitidos.
- Ayuda a planear qué exploits o payloads funcionarán.

---

## 5. Ver perfiles del firewall
```bash
C:\> netsh advfirewall show allprofiles
```
- Muestra estado del firewall en cada perfil:
  - **Domain**
  - **Private**
  - **Public**
- Da idea del nivel de restricción en diferentes escenarios.

---
