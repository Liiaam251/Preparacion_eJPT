# Enumeración de Servicios y Procesos en Windows

Después de obtener una sesión en una máquina víctima (con Meterpreter), es esencial identificar procesos y servicios en ejecución. Esto ayuda a detectar software de seguridad, posibles puntos de migración y aplicaciones vulnerables.

---

## 1. Listar procesos activos
```bash
meterpreter > ps
```
- Muestra todos los procesos en ejecución.
- Incluye PID, nombre y usuario propietario.
- Sirve para elegir un proceso estable en el cual **migrar** (ej: `explorer.exe`).

---

## 2. Buscar un proceso específico
```bash
meterpreter > pgrep explorer.exe
```
- Devuelve el **PID** del proceso indicado (aquí `explorer.exe`).
- `explorer.exe` es un proceso del usuario logueado, ideal para migrar.

---

## 3. Migrar a un proceso
```bash
meterpreter > migrate <PID>
```
- Cambia la inyección de Meterpreter a otro proceso.
- Se hace para estabilizar la sesión o escalar privilegios.
- Ejemplo:  
```bash
meterpreter > migrate 2448
```

---

## 4. Enumerar servicios con WMIC
```bash
C:\> wmic service list brief
```
- Lista servicios instalados.
- Muestra nombre, estado, tipo de inicio.
- Útil para detectar servicios vulnerables o inseguros.

---

## 5. Ver procesos con servicios asociados
```bash
C:\> tasklist /SVC
```
- Lista procesos y los servicios que corren dentro de cada uno.
- Permite identificar qué servicio corresponde a un proceso.
- Ejemplo: ver si un antivirus está activo.

---

## Resumen de comandos
```bash
ps                         # Procesos activos en Meterpreter
pgrep explorer.exe         # Buscar PID del proceso explorer.exe
migrate <PID>              # Migrar al proceso indicado
wmic service list brief    # Listar servicios instalados
tasklist /SVC              # Ver procesos con servicios asociados
```
