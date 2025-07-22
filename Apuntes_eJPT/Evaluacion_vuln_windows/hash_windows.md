# Hashes de contraseñas en Windows

---

## ¿Qué son los hashes de contraseñas?

- En lugar de guardar contraseñas en texto plano, Windows almacena un **hash**, es decir, una representación irreversible calculada a partir de la contraseña.
- Durante el inicio de sesión, Windows calcula el hash de la contraseña introducida y lo compara con el almacenado.

---

# Ubicación de los hashes

### SAM (Security Account Manager)
- Los hashes locales de las cuentas de usuario se guardan en el archivo:
```
C:\Windows\System32\config\SAM
```
- Este archivo está protegido y sólo puede accederse con privilegios de SYSTEM.

### NTDS.dit
- En un **Controlador de Dominio**, los hashes del dominio se almacenan en:
```
C:\Windows\NTDS\NTDS.dit
```
- También requiere privilegios elevados para acceder.

### Cache
- Windows también guarda hashes en caché para iniciar sesión sin conexión:
  - Archivo: `C:\Windows\System32\config\SECURITY`

---

# Tipos de hashes en Windows

### LM Hash (Lan Manager)
- Muy antiguo y débil.
- Divide la contraseña en bloques de 7 caracteres y convierte todo a mayúsculas.
- Es vulnerable a ataques por diccionario y rainbow tables.
- Desde Windows Vista está deshabilitado por defecto.

### NTLM Hash
- Sustituto de LM.
- Algoritmo de hash basado en MD4 aplicado a la contraseña Unicode.
- Más seguro que LM, pero sigue siendo vulnerable a ataques offline.

### NTLMv2
- Versión mejorada de NTLM para autenticación en red, con un reto-respuesta más robusto.
- Aún así, sigue siendo menos seguro que Kerberos.

### Kerberos
- Protocolo por defecto en redes Windows desde Active Directory.
- Usa claves derivadas de la contraseña en un sistema de tickets.

---

# Cómo obtener los hashes

### Desde un sistema comprometido
Con privilegios de SYSTEM o Administrador, se pueden extraer los hashes.

#### Con `mimikatz`
```
privilege::debug
lsadump::sam
```

#### Con `pwdump`
Extrae los hashes de la SAM:
```
pwdump > hashes.txt
```

#### Con `secretsdump.py` (Impacket)
Extrae hashes locales o del dominio:
```
secretsdump.py -sam SAM -system SYSTEM LOCAL
```
o desde un Controlador de Dominio:
```
secretsdump.py 'DOMINIO/usuario:contraseña@ip'
```

---

# Formato típico del hash

```
Administrador:500:NO PASSWORD*********************:AAD3B435B51404EEAAD3B435B51404EE:31D6CFE0D16AE931B73C59D7E0C089C0:::
```

- `AAD3B435B51404EEAAD3B435B51404EE` → hash LM (vacío si está deshabilitado)
- `31D6CFE0D16AE931B73C59D7E0C089C0` → hash NTLM

---

# Ataques comunes contra los hashes

- **Fuerza bruta o diccionario**
  - Probar combinaciones para encontrar la contraseña original.
- **Pass-the-Hash (PtH)**
  - Usar el hash directamente para autenticarse en otro sistema sin conocer la contraseña.
- **Cracking offline**
  - Extraer los hashes y romperlos en un sistema separado con herramientas como Hashcat o John the Ripper.

---

# Mitigaciones

- Deshabilitar LM hashes (`NoLMHash` en el registro).
- Usar contraseñas largas y complejas.
- Habilitar autenticación Kerberos siempre que sea posible.
- Restringir el almacenamiento en caché de credenciales.
- Controlar el acceso físico y lógico al sistema.

---

# Herramientas útiles

| Herramienta       | Uso principal                  |
|-------------------|--------------------------------|
| mimikatz          | Extraer y manipular credenciales en memoria |
| secretsdump.py    | Extraer hashes de SAM o NTDS.dit |
| pwdump            | Extraer hashes de la SAM       |
| Hashcat           | Cracking de hashes             |
| John the Ripper   | Cracking de hashes             |

---
