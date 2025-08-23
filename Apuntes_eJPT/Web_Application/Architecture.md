# Web Application Architecture

---

## 1. Definición
La **arquitectura de aplicaciones web** describe cómo los distintos componentes (cliente, servidor, base de datos, servicios externos) interactúan entre sí para que una aplicación web funcione.  
Es clave en **pentesting web** porque cada capa puede tener vulnerabilidades diferentes.

---

## 2. Componentes principales

### 1. Cliente (Frontend)
- Normalmente un **navegador web**.
- Interactúa con la aplicación a través de **HTTP/HTTPS**.
- Tecnologías comunes: **HTML, CSS, JavaScript, frameworks (React, Angular, Vue)**.

### 2. Servidor Web (Backend)
- Recibe peticiones del cliente y responde con contenido.
- Procesa lógica de la aplicación.
- Lenguajes comunes: **PHP, Python (Django/Flask), Node.js, Java (Spring), .NET**.

### 3. Base de Datos
- Almacena información persistente de la aplicación.
- Tipos:
  - **Relacionales (SQL):** MySQL, PostgreSQL, SQL Server.
  - **NoSQL:** MongoDB, Redis.
- Comunicación con el backend mediante **queries**.

### 4. Servicios Externos / APIs
- Integraciones con terceros (ej: pagos con PayPal, OAuth con Google).
- Se accede vía **REST APIs**, **SOAP**, **GraphQL**.

---

## 3. Modelos de arquitectura

### Arquitectura de 2 capas
- Cliente ↔ Servidor con base de datos.
- Simple pero poco escalable.

### Arquitectura de 3 capas
- Cliente (Frontend) ↔ Servidor de aplicación (lógica) ↔ Base de datos.
- Modelo más usado en aplicaciones modernas.

### Arquitecturas avanzadas
- **Microservicios:** cada módulo es independiente y se comunica por API.  
- **Serverless:** funciones en la nube que se ejecutan bajo demanda.  
- **CDN (Content Delivery Network):** distribuye contenido estático cerca del usuario.

---

## 4. Flujo típico de una petición web
1. El cliente envía una petición HTTP/HTTPS (GET/POST).  
2. El servidor web procesa la petición.  
3. Si es necesario, consulta la base de datos.  
4. Devuelve la respuesta al cliente (HTML, JSON, etc.).  
5. El navegador renderiza el contenido.  

---

## 5. Seguridad en cada capa
- **Frontend (cliente):**
  - Validación insegura en JavaScript.
  - XSS (Cross-Site Scripting).
- **Servidor (backend):**
  - Lógica de negocio vulnerable.
  - Inyecciones (SQLi, NoSQLi).
  - RCE (Remote Code Execution).
- **Base de datos:**
  - SQL Injection.
  - Configuración débil (default passwords).
- **APIs/Servicios externos:**
  - API Keys expuestas.
  - Falta de autenticación/autorización.
  - Inseguridad en CORS.

---

## 6. Resumen visual (3-tier architecture)

```
Cliente (Browser) 
     |
     v
Servidor Web / Aplicación
     |
     v
Base de Datos
```


