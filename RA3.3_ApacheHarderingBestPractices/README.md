# Práctica 3.3: Apache Hardening (Best Practices)

## 📖 Descripción
Aplicación de técnicas de endurecimiento (hardening) en un servidor web Apache siguiendo las recomendaciones de seguridad de la industria (Geekflare/OWASP). El objetivo es reducir la superficie de ataque ocultando información sensible y mitigando ataques del lado del cliente mediante cabeceras HTTP específicas.

Docker: https://hub.docker.com/repository/docker/rubjargon/apache-hardened/general

## 🛠️ Técnicas de Hardening Aplicadas

### 1. Ocultación de Información (Security by Obscurity)
* **ServerTokens Prod:** Configurado para que la cabecera `Server` solo devuelva "Apache", ocultando la versión exacta y el sistema operativo (Ubuntu) para dificultar la búsqueda de vulnerabilidades (CVEs) específicos.
* **ServerSignature Off:** Elimina la firma del servidor (versión y SO) en las páginas de error generadas automáticamente (404, 500, etc.).

### 2. Deshabilitación de Listado de Directorios
* Se ha eliminado la opción `Indexes` de la configuración principal (`apache2.conf`). Esto impide que, si falta un archivo `index.html`, el servidor muestre el listado de todos los archivos y carpetas del directorio, protegiendo la estructura interna de la web.

### 3. Cabeceras de Seguridad HTTP (Security Headers)
Se ha habilitado el módulo `mod_headers` para inyectar las siguientes protecciones en cada respuesta HTTP:
* **X-Frame-Options: SAMEORIGIN**: Previene ataques de *Clickjacking* impidiendo que la web se cargue en iframes de otros sitios no autorizados.
* **X-XSS-Protection: 1; mode=block**: Activa el filtro de *Cross-Site Scripting* (XSS) incorporado en los navegadores antiguos para bloquear la carga de la página si se detecta un ataque.
* **X-Content-Type-Options: nosniff**: Evita que el navegador intente "adivinar" el tipo de archivo (MIME sniffing), forzando a respetar el `Content-Type` declarado por el servidor.

## 🚀 Instrucciones de Despliegue

### 1. Construir la imagen
```bash
docker build -t apache-hardened .
```

### 2. Arrancar el contenedor
Se expone en el puerto **8088** para pruebas.
```bash
docker run -d -p 8088:80 --name mi-apache-hardened apache-hardened
```

## ✅ Evidencias de Verificación

Se ha verificado mediante el comando `curl -I` que la configuración de seguridad es efectiva.

**Resultado de la auditoría:**
1.  La cabecera `Server` no muestra la versión (solo "Apache").
2.  Las cabeceras de seguridad (`X-Frame-Options`, `X-XSS`, `X-Content-Type`) están presentes en la respuesta.

![Evidencia de Hardening con Curl](imagen.png)

## ☁️ Docker Hub
La imagen securizada ha sido subida al repositorio público:
```bash
docker login
docker tag apache-hardened rubjargon/apache-hardened:v1
docker push rubjargon/apache-hardened:v1
```

---
**Autor:** rubjargon
