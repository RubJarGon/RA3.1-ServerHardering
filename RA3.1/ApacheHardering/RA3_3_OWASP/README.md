# Práctica 3: Apache WAF + ModSecurity + OWASP CRS

## 📖 Descripción
Esta práctica consiste en la creación de una imagen Docker basada en Ubuntu 22.04 que despliega un servidor web **Apache** securizado. 

Se ha integrado el módulo **ModSecurity** y se han configurado las reglas **OWASP Core Rule Set (CRS v3.3.5)** para actuar como un Web Application Firewall (WAF), protegiendo el servidor contra ataques comunes.

Docker: https://hub.docker.com/repository/docker/rubjargon/apache-waf-owasp/general

## 🛠️ Tecnologías Utilizadas
* **Base:** Ubuntu 22.04
* **Servidor Web:** Apache2
* **WAF:** libapache2-mod-security2
* **Reglas:** OWASP CRS v3.3.5 (Instalación manual para asegurar compatibilidad).

## 🚀 Instrucciones de Despliegue

### 1. Construir la imagen
```bash
docker build -t apache-owasp .
```

### 2. Arrancar el contenedor
```bash
docker run -d -p 8080:80 --name mi-waf-owasp apache-owasp
```

## ✅ Verificación de Seguridad
Para comprobar que las reglas OWASP están activas y bloqueando ataques, se realizan las siguientes pruebas con `curl`.

**1. Acceso Legítimo (Debe permitir el acceso - HTTP 200)**
```bash
curl -I http://localhost:8080
```
> Resultado esperado: `HTTP/1.1 200 OK`

**2. Prueba de ataque XSS (Debe bloquear - HTTP 403)**
Simulación de inyección de JavaScript en la URL:
```bash
curl -I "http://localhost:8080/?q=<script>alert(1)</script>"
```
> Resultado esperado: `HTTP/1.1 403 Forbidden`

**3. Prueba de ataque SQL Injection (Debe bloquear - HTTP 403)**
Simulación de inyección SQL básica (con espacios codificados):
```bash
curl -I "http://localhost:8080/?id=1'%20OR%20'1'='1"
```
> Resultado esperado: `HTTP/1.1 403 Forbidden`

### 📸 Evidencia del Bloqueo
A continuación se muestra la captura de pantalla demostrando que el servidor devuelve un **403 Forbidden** ante los ataques:

![Captura del bloqueo 403](imagen_1.png)


---
**Autor:** rubjargon
