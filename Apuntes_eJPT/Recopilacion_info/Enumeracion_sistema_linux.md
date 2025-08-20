# Linux – System Enumeration

Tras obtener acceso inicial a un sistema Linux, es fundamental recolectar información básica del host para evaluar su contexto y posibles vectores de escalada de privilegios.

---

## 1. Información del sistema con Meterpreter
Si la sesión es de **Meterpreter** en Linux:
```bash
meterpreter > sysinfo
```
- Muestra nombre del host, arquitectura y sistema operativo detectado.

---

## 2. Nombre del host
```bash
hostname
```
- Devuelve el nombre del sistema.
- Útil para identificar la máquina dentro de la red.

---

## 3. Información de distribución
```bash
cat /etc/issue
cat /etc/*release
```
- Muestra la distribución (Debian, Ubuntu, CentOS, RedHat, etc.).
- Indica versión, release y a veces nombre en clave.

Ejemplo de salida:
```
Ubuntu 20.04.3 LTS \n \l
DISTRIB_ID=Ubuntu
DISTRIB_RELEASE=20.04
DISTRIB_CODENAME=focal
DISTRIB_DESCRIPTION="Ubuntu 20.04.3 LTS"
```

---

## 4. Kernel
```bash
uname -a
```
- Información detallada del kernel y compilación.

```bash
uname -r
```
- Versión exacta del kernel (clave para buscar exploits locales).

---

## 5. Variables de entorno
```bash
env
```
- Muestra variables de entorno del sistema.
- Importante para:
  - Rutas (`PATH`)
  - Usuarios conectados (`USER`, `HOME`)
  - Configuraciones que pueden dar pistas de servicios.


