# Volcado de Hashes con Mimikatz (Kiwi) en Metasploit

## ¿Qué es Kiwi?

**Kiwi** es un plugin de Meterpreter que integra funcionalidades del famoso proyecto **Mimikatz** dentro de una sesión de Metasploit. Permite extraer credenciales, volcar hashes de SAM, secrets y más.

---

## Requisitos

- Acceso a una **sesión Meterpreter** con privilegios de **NT AUTHORITY\SYSTEM** o **administrador con SeDebugPrivilege**.
- Tener cargado el plugin `kiwi`.
- migrar a pgrep lsass

````
migrate xxxx
````
---

## Cargar el plugin Kiwi

```bash
load kiwi
```

Esto habilita los comandos que replican las funciones de Mimikatz directamente desde Meterpreter.

---

## Enumerar todas las credenciales conocidas

```bash
creds_all
```

- Muestra todas las credenciales disponibles en memoria (plaintext, NTLM hashes, tickets Kerberos, etc).
- Ideal para sesiones donde los usuarios tienen sesiones activas.

---

## Volcar el archivo SAM (`lsa_dump_sam`)

```bash
lsa_dump_sam
```

- Extrae hashes locales del archivo **SAM** (Security Account Manager).
- Incluye usuarios locales como `Administrator`, `Guest`, etc.

---

## Volcar secrets de LSA (`lsa_dump_secrets`)

```bash
lsa_dump_secrets
```

- Extrae **secretos almacenados en el registro de Windows**, como contraseñas de servicios, VPN, tareas programadas, etc.

---

## Interpretar el resultado

- Los **hashes** pueden usarse para ataques como **Pass-the-Hash**.
- Las **contraseñas en texto claro** pueden facilitar el movimiento lateral o escalado de privilegios.
- Los **secretos de LSA** pueden contener contraseñas de cuentas de dominio o credenciales de red.

---

## Consejos

- Si `lsa_dump_sam` o `lsa_dump_secrets` no funcionan, asegúrate de haber migrado a un proceso con los permisos adecuados (por ejemplo: `lsass.exe`) o tener privilegios de SYSTEM.
- Puedes usar `ps`, `pgrep lsass`, y `migrate <PID>` si es necesario.

