# Práctica 5: Servidor Nginx Securizado (Hardening Completo)

## 📖 Descripción
Despliegue de un servidor **Nginx** utilizando Docker (base **Debian 12 Bookworm**) con una configuración integral de seguridad. Este entorno replica y mejora el endurecimiento realizado previamente en Apache, adaptándolo a la arquitectura de alto rendimiento de Nginx.

Docker: https://hub.docker.com/repository/docker/rubjargon/nginx-hardened/general

## 🛠️ Tecnologías y Configuración
* **Base:** Debian 12 (Bookworm-slim) para evitar problemas de repositorios y asegurar estabilidad.
* **PHP 8.2 FPM:** Procesamiento de scripts dinámicos a través de socket UNIX.
* **WAF (ModSecurity):** Integración nativa con reglas **OWASP CRS v3.3.5** para mitigar ataques web (SQLi, XSS, etc.).
* **SSL/TLS:** Certificado autofirmado para habilitar HTTPS en el puerto 443.
* **Hardening Web:**
    * **HSTS:** Cabecera `Strict-Transport-Security` para forzar conexiones seguras.
    * **CSP:** Cabecera `Content-Security-Policy` para restringir orígenes de contenido.
    * **Auth Básica:** Protección del directorio `/privado` mediante archivo `.htpasswd`.

## 🚀 Instrucciones de Despliegue

### 1. Construir la imagen
```bash
docker build -t nginx-hardened .
```

### 2. Arrancar el contenedor
Se utilizan los puertos **8081** (HTTP) y **8443** (HTTPS) para evitar conflictos con el sistema anfitrión.
```bash
docker run -d -p 8081:80 -p 8443:443 --name mi-nginx-seguro nginx-hardened
```

## ✅ Evidencias de Verificación

### 1. PHP Info
Acceso a: `http://localhost:8081/index.php`
* **Resultado:** Se visualiza la tabla de información de PHP 8.2, confirmando que Nginx procesa archivos `.php` correctamente a través de FastCGI.

![Evidencia PHP Info](imagen_2.png)

### 2. Seguridad en Red (Headers)
Verificación mediante terminal:
```bash
curl -I -k https://localhost:8443
```
* **Resultado:** El servidor devuelve estado `200 OK` e incluye las cabeceras de seguridad `Strict-Transport-Security` y `Content-Security-Policy`.

![Evidencia Curl Headers](imagen_1.png)

### 3. Área Restringida
Acceso a: `http://localhost:8081/privado/`
* **Resultado:** El navegador solicita credenciales de acceso (Usuario: `usuario` / Contraseña: `password123`). Tras introducirse correctamente, se muestra el contenido protegido.

![Evidencia Area Privada](imagen_3.png)



