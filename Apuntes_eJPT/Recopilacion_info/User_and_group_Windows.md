# Enumeración de Usuarios y Grupos en Windows con Meterpreter

## 1. Ver el usuario actual
```bash
meterpreter > getuid
```

---

## 2. Ver todos los usuarios logueados
1. Mandar la sesión a segundo plano:
```bash
meterpreter > background
```

2. Cargar módulo de usuarios logueados:
```bash
use post/windows/gather/enum_logged_on_users
set SESSION 1
run
```

---

## 3. Ver todos los usuarios locales de Windows
Entrar en shell desde Meterpreter:
```bash
meterpreter > shell
C:\> net user
```

---

## 4. Ver información detallada de un usuario
```bash
C:\> net user <usuario>
```

Ejemplo:
```bash
C:\> net user Administrator
```

---

## 5. Ver todos los grupos locales
```bash
C:\> net localgroup
```

---

## 6. Ver miembros de un grupo específico
```bash
C:\> net localgroup Administrators
```

---

## 7. Otro módulo para enumerar usuarios y grupos
```bash
use post/windows/gather/enum_ad_computers
set SESSION 1
run
```

```bash
use post/windows/gather/enum_ad_groups
set SESSION 1
run
```

---

## 8. Resumen de Comandos Clave
```bash
getuid                       # Usuario actual
net user                     # Lista de usuarios
net user <usuario>           # Info detallada de un usuario
net localgroup               # Lista de grupos
net localgroup Administrators # Miembros del grupo Administradores
```
