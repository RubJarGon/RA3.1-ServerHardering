# RA3.1: Apache Hardening - CSP & HSTS

**Autor:** RubJarGon
**Asignatura:** Seguridad / Despliegue de Aplicaciones Web
**Estado:** ✅ Completado

## 📋 Descripción de la Actividad
El objetivo de esta práctica es asegurar (harden) el servidor web Apache mediante la implementación de cabeceras de seguridad HTTP estrictas. Se ha configurado el servidor para protegerse contra ataques de Cross-Site Scripting (XSS) y para forzar conexiones seguras mediante HTTPS.

Docker: https://hub.docker.com/repository/docker/rubjargon/pr1/general

## 🛡️ Medidas de Seguridad Implementadas

### 1. Content Security Policy (CSP)
Se ha configurado la cabecera `Content-Security-Policy` para restringir los orígenes de contenido permitidos.

* **Configuración:** `default-src 'self';`
* **Función:** Esta directiva instruye al navegador para que solo ejecute scripts, estilos e imágenes que provengan del mismo origen (mismo servidor). Esto mitiga significativamente el riesgo de inyección de contenido malicioso (XSS).

### 2. HTTP Strict Transport Security (HSTS)
Se ha habilitado HSTS para forzar a los navegadores a interactuar con el servidor únicamente a través de conexiones HTTPS seguras.

* **Configuración:** `max-age=63072000; includeSubDomains`
* **Función:**
    * `max-age=63072000`: Indica al navegador que recuerde acceder solo por HTTPS durante los próximos 2 años.
    * `includeSubDomains`: Aplica esta regla a todos los subdominios presentes y futuros.

## 📸 Verificación y Evidencia

La siguiente captura de pantalla muestra las herramientas de desarrollo del navegador (pestaña Red/Network) confirmando la recepción de las cabeceras configuradas tras una petición `GET` a `https://localhost:8081/`.

![Evidencia de Hardening](imagen_1.png)

### Detalles del Entorno
* **Servidor Web:** Apache/2.4.66 (Unix)
* **Librería Criptográfica:** OpenSSL/3.5.4
* **Protocolo:** HTTPS / HTTP 1.1
* **Sistema Operativo Cliente:** Ubuntu Linux (x86_64)

## ⚙️ Configuración del Servidor (Fragmento)

Las líneas añadidas al archivo de configuración de Apache (`httpd.conf` o VirtualHost) son:

```apache
<IfModule mod_headers.c>
    # Configuración de Content Security Policy
    Header always set Content-Security-Policy "default-src 'self';"

    # Configuración de HSTS (2 años)
    Header always set Strict-Transport-Security "max-age=63072000; includeSubDomains"
</IfModule>
