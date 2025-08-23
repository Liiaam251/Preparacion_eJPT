# Protocolo HTTP

---

## 1. Definición
**HTTP (HyperText Transfer Protocol)** es un protocolo de comunicación en la capa de aplicación del modelo OSI.  
- Es el protocolo base de la **World Wide Web**.  
- Permite la transferencia de recursos como páginas HTML, imágenes, videos, APIs, etc.  
- Usa normalmente el **puerto 80** (HTTP) y **443** (HTTPS, versión segura con TLS/SSL).  

---

## 2. Características principales
- **Basado en texto** → fácil de interpretar con herramientas como BurpSuite o curl.  
- **Modelo cliente-servidor** → el cliente (navegador) envía peticiones y el servidor responde.  
- **Sin estado (stateless)** → cada petición es independiente (se usa **cookies o tokens** para mantener sesiones).  
- **Extensible** → soporta métodos, cabeceras y códigos de estado.  

---

## 3. Métodos HTTP más comunes
- `GET` → Solicita un recurso (ej: una página web).  
- `POST` → Envía datos al servidor (ej: formularios, login).  
- `PUT` → Actualiza un recurso existente.  
- `DELETE` → Elimina un recurso.  
- `HEAD` → Como GET pero solo devuelve cabeceras.  
- `OPTIONS` → Muestra los métodos soportados por el servidor.  
- `PATCH` → Actualiza parcialmente un recurso.  

---

## 4. Estructura de una petición HTTP
Ejemplo de **petición GET**:
```
GET /index.html HTTP/1.1
Host: www.ejemplo.com
User-Agent: Mozilla/5.0
Accept: text/html
Cookie: sessionid=abc123
```

Componentes:
1. **Línea de petición** → Método, recurso, versión de HTTP.  
2. **Cabeceras** → Información adicional (user-agent, cookies, etc.).  
3. **Cuerpo** (opcional) → Datos enviados en métodos como `POST`.  

---

## 5. Estructura de una respuesta HTTP
Ejemplo:
```
HTTP/1.1 200 OK
Date: Sun, 20 Aug 2023 12:00:00 GMT
Server: Apache/2.4.41 (Ubuntu)
Content-Type: text/html
Content-Length: 1234

<html>
  <h1>Bienvenido</h1>
</html>
```

Componentes:
1. **Línea de estado** → Versión HTTP, código y mensaje.  
2. **Cabeceras de respuesta** → Info del servidor, tipo de contenido, longitud, cookies.  
3. **Cuerpo** → Contenido del recurso solicitado (HTML, JSON, etc.).  

---

## 6. Códigos de estado HTTP
- **1xx Informativos** → 100 Continue, 101 Switching Protocols.  
- **2xx Éxito** → 200 OK, 201 Created, 204 No Content.  
- **3xx Redirecciones** → 301 Moved Permanently, 302 Found, 304 Not Modified.  
- **4xx Errores del cliente** → 400 Bad Request, 401 Unauthorized, 403 Forbidden, 404 Not Found.  
- **5xx Errores del servidor** → 500 Internal Server Error, 502 Bad Gateway, 503 Service Unavailable.  

---

## 7. Seguridad HTTP
- **HTTP (80)** → sin cifrado, vulnerable a sniffing.  
- **HTTPS (443)** → cifrado con TLS/SSL, protege confidencialidad e integridad.  
- **Cabeceras de seguridad comunes:**
  - `Strict-Transport-Security` (HSTS).  
  - `X-Frame-Options`.  
  - `Content-Security-Policy` (CSP).  

---

## 8. Herramientas útiles
- **curl** → probar peticiones manuales.  
```bash
curl -v http://ejemplo.com
```

- **BurpSuite** → interceptar y modificar tráfico HTTP.  
- **Nmap NSE** → detección de servicios HTTP.  
```bash
nmap -p80 --script http-enum <IP>
```

---

## 9. Resumen rápido
- Cliente envía → método + URL + cabeceras + datos.  
- Servidor responde → código de estado + cabeceras + contenido.  
- Métodos principales: GET, POST, PUT, DELETE.  
- HTTP es **sin estado**, HTTPS añade cifrado.  
