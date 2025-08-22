# Dumping & Cracking de Hashes NTLM en Windows

---

## 1. Obtención de hashes NTLM
Después de ganar acceso a una máquina Windows (ejemplo: con Meterpreter):

```bash
hashdump
```

Esto mostrará los usuarios y sus hashes NTLM, por ejemplo:
```
Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
student:1001:aad3b435b51404eeaad3b435b51404ee:cc36cf7a8514893efccd3324464t5932:::
```

- El segundo valor largo (después de `:500:`) es el **NT hash**.  
- Copiar el hash del usuario que se desea crackear y guardarlo en un archivo, por ejemplo `hash.txt`.

---

## 2. Cracking con John the Ripper
John es una de las herramientas más usadas para romper hashes.

### Uso básico
```bash
john --format=NT hash.txt
```

### Uso con diccionario (rockyou.txt)
```bash
john --format=NT --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
```

### Ver resultados después del crack
```bash
john --show hash.txt
```

---

## 3. Cracking con Hashcat
Hashcat es más rápido y soporta GPU.

### Modo de ataque con diccionario
```bash
hashcat -m 1000 -a 0 hash.txt /usr/share/wordlists/rockyou.txt
```

### Parámetros clave
- `-m 1000` → NTLM hash mode.  
- `-a 0` → ataque con diccionario (wordlist).  
- `-a 3` → ataque de fuerza bruta.  

Ejemplo de fuerza bruta:
```bash
hashcat -m 1000 -a 3 hash.txt ?a?a?a?a
```
(`?a` = cualquier caracter ASCII, aquí fuerza 4 caracteres)

---

## 4. Flujo recomendado
1. **Dumping de hashes** → `hashdump`.  
2. **Guardar hash** en archivo `hash.txt`.  
3. **Crackear con diccionario**:
   - Primero con John.  
   - Si no funciona, probar con Hashcat.  
4. **Ver credenciales en claro** → usar `john --show` o resultados de Hashcat.  
5. **Usar credenciales obtenidas** para moverse lateralmente o elevar privilegios.  

---

## 5. Utilidad en post-explotación
- Permite **recuperar contraseñas en texto plano**.  
- Facilita **movimiento lateral** entre máquinas si la misma contraseña se reutiliza.  
- Con credenciales administrativas → **control total** sobre el dominio o la red.  
