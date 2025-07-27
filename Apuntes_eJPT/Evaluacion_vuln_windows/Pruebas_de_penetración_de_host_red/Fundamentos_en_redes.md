#  Fundamentos de Redes y Modelo OSI

##  ¿Qué es una red?

Una red informática es un conjunto de dispositivos conectados entre sí que intercambian información mediante protocolos de comunicación. Permiten compartir recursos (archivos, impresoras, Internet, etc.).

---

##  Componentes básicos de una red

| Componente    | Función                                           |
|---------------|---------------------------------------------------|
| **Host**      | Dispositivo conectado a la red (PC, móvil, etc.) |
| **Switch**    | Conecta varios dispositivos dentro de una red LAN |
| **Router**    | Conecta redes diferentes y enruta tráfico         |
| **Firewall**  | Controla el tráfico que entra o sale              |
| **NIC**       | Tarjeta de red en cada dispositivo                |
| **Access Point** | Proporciona conectividad inalámbrica          |

---

##  Tipos de redes

- **LAN (Local Area Network):** Red local, pequeña (una oficina, casa).
- **WAN (Wide Area Network):** Red amplia (como Internet).
- **MAN (Metropolitan Area Network):** Red de ciudad o campus.
- **PAN (Personal Area Network):** Alcance de pocos metros (Bluetooth).
- **WLAN:** LAN inalámbrica (Wi-Fi).

---

##  Direccionamiento IP

- **IPv4:** Formato de 32 bits (ej: 192.168.1.1).
- **IPv6:** Formato de 128 bits (ej: fe80::1).
- **IP pública:** Visible en Internet.
- **IP privada:** Usada internamente (ej: 192.168.x.x).

---

##  Modelo OSI (Open Systems Interconnection)

Modelo de referencia que divide la comunicación en **7 capas**, de más abstracta a más física:

| Capa | Nombre               | Función principal                         | Ejemplos                   |
|------|----------------------|-------------------------------------------|----------------------------|
| 7    | Aplicación           | Interacción directa con el usuario        | HTTP, FTP, DNS, SSH        |
| 6    | Presentación         | Formato de datos y cifrado                | JPEG, SSL/TLS, ASCII       |
| 5    | Sesión               | Control de sesión entre dispositivos      | RPC, NetBIOS               |
| 4    | Transporte           | Transporte confiable o no                 | TCP, UDP                   |
| 3    | Red                  | Enrutamiento entre redes                  | IP, ICMP, ARP              |
| 2    | Enlace de datos      | Control de acceso al medio físico         | Ethernet, PPP, MAC         |
| 1    | Física               | Transmisión de bits por el medio          | Cables, Wi-Fi, voltaje     |

>  En la práctica, las capas suelen estar combinadas o simplificadas, especialmente en el modelo TCP/IP (de 4 capas).

---

##  Modelo TCP/IP vs OSI

| TCP/IP           | OSI equivalente           |
|------------------|---------------------------|
| Aplicación       | Aplicación, Presentación, Sesión |
| Transporte       | Transporte                |
| Internet         | Red                       |
| Acceso a red     | Enlace de datos + Física  |

---

##  Protocolos comunes

| Protocolo | Capa | Descripción                          |
|-----------|------|--------------------------------------|
| HTTP      | 7    | Navegación web                       |
| HTTPS     | 7    | Web segura (cifrado SSL/TLS)         |
| FTP       | 7    | Transferencia de archivos            |
| SSH       | 7    | Acceso remoto seguro                 |
| DNS       | 7    | Resolución de nombres                |
| TCP       | 4    | Conexión confiable (handshake)       |
| UDP       | 4    | Conexión no confiable (rápida)       |
| IP        | 3    | Enrutamiento entre redes             |
| ARP       | 2-3  | Resolución IP → MAC                  |
| Ethernet  | 2    | Trama de datos en LAN                |
| Wi-Fi     | 1-2  | Transmisión inalámbrica              |

---

##  Otros conceptos clave

- **MAC Address:** Dirección física única de cada NIC.
- **Gateway:** Puerta de enlace para salir de una red local.
- **Subnet:** División lógica de redes para segmentación.
- **NAT (Network Address Translation):** Traducción de IP privada ↔ pública.
- **DHCP:** Asigna direcciones IP automáticamente.


