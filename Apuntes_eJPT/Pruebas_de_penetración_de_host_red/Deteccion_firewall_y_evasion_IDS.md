# Detección de Firewalls y Evasión de IDS/IPS

## ¿Qué es un Firewall?

Un firewall es un sistema que filtra el tráfico de red según reglas. Su función principal es permitir o bloquear tráfico hacia/desde una red.

### Tipos comunes:
- **Estáticos (stateless):** No consideran el estado de las conexiones.
- **Dinámicos (stateful):** Monitorean el estado de la conexión.
- **Firewalls de aplicación:** Operan en la capa 7 del modelo OSI.
- **Personales o de host:** Ejecutados en un sistema individual.

---

## ¿Qué es un IDS/IPS?

- **IDS (Intrusion Detection System):** Detecta actividades sospechosas.
- **IPS (Intrusion Prevention System):** Detecta y bloquea tráfico malicioso en tiempo real.

Ambos pueden trabajar con reglas de firmas, comportamiento anómalo y análisis de protocolos.

---

## Detección de Firewalls con Nmap

### Indicadores de firewall:
- Todos los puertos aparecen como `filtered`.
- No hay respuestas ICMP (echo-request bloqueado).
- Caída en la tasa de paquetes recibidos.

### Comando básico:
```bash
nmap -sS <IP>
```

---

## Técnicas de evasión con Nmap

### `-f` → Fragmentación de paquetes
Fragmenta los paquetes IP para evadir inspecciones.

```bash
nmap -sS -f <IP>
```

---

### `-F` → Fast scan
Escanea solo los puertos más comunes (100 más usados).

```bash
nmap -F <IP>
```

---

### `--data-length` → Inserta datos aleatorios
Añade bytes al payload TCP/UDP para alterar la firma.

```bash
nmap -sS --data-length 200 <IP>
```

---

### `-D` → Decoys (señuelos)
Oculta tu dirección IP entre otras IPs falsas.

```bash
nmap -sS -D 192.168.1.10,192.168.1.15,ME <IP>
```

O aleatorias:

```bash
nmap -sS -D RND:10 <IP>
```

---

### Combinaciones posibles

```bash
nmap -sS -f --data-length 200 -D RND:10 <IP>
```

Esta combinación fragmenta, altera el contenido y disfraza el origen del escaneo.

---

## Otras técnicas de evasión

| Técnica                      | Uso                                         |
|-----------------------------|----------------------------------------------|
| `--scan-delay 1s`           | Introduce retrasos entre paquetes.          |
| `--max-rate 10`             | Limita la velocidad de envío.               |
| `--source-port 53`          | Usa puertos comunes (DNS, HTTP).            |
| `--ttl`                     | Modifica el TTL para evadir ciertos filtros |
| `--badsum`                  | Envía checksums incorrectos (test IDS)      |

---

## Ejemplos prácticos con Nmap

### 1. Escaneo de puertos TCP con detección de firewall
```bash
nmap -sS -Pn -vv <IP>
```

### 2. Escaneo UDP
```bash
nmap -sU <IP>
```

### 3. Escaneo de versión de servicios con evasión
```bash
nmap -sV -f --data-length 100 <IP>
```

### 4. Escaneo agresivo con evasión
```bash
nmap -A -T2 -f --data-length 150 -D RND:5 <IP>
```

### 5. Escaneo lento para evitar IDS
```bash
nmap -sS --scan-delay 2s --max-rate 10 <IP>
```

### 6. Escaneo con puerto de origen DNS
```bash
nmap -sS --source-port 53 <IP>
```
