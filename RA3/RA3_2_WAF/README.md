# RA3.2: Web Application Firewall (WAF) - Apache & ModSecurity

**Autor:** RubJarGon
**Asignatura:** Seguridad / Despliegue de Aplicaciones Web
**Imagen Base:** `apache-hardening` (RA3.1)
**Estado:** ✅ Completado

## 📋 Descripción de la Actividad
El objetivo de esta práctica es añadir una capa de defensa activa al servidor web Apache asegurado previamente. Se ha implementado un **WAF (Web Application Firewall)** utilizando **ModSecurity v3** junto con las reglas estándar de la industria **OWASP Core Rule Set (CRS)**.

El sistema ahora es capaz de detectar y bloquear ataques en tiempo real, como Inyecciones SQL (SQLi), Cross-Site Scripting (XSS) y otros vectores definidos en el OWASP Top 10.

Docker: https://hub.docker.com/repository/docker/rubjargon/apache-waf/general

## 🏗️ Arquitectura en Cascada (Docker)
Este contenedor sigue una estrategia de herencia de imágenes para mantener la modularidad:

1.  **Nivel 1 (RA3.1):** Imagen base con Hardening (SSL/TLS, HSTS, CSP).
2.  **Nivel 2 (RA3.2):** Se construye sobre la anterior (`FROM apache-hardening`) y añade el motor WAF.

## ⚙️ Implementación Técnica

### 1. Motor ModSecurity
Se ha instalado el módulo `libapache2-mod-security2`.
* **Configuración:** `SecRuleEngine On`
* **Acción:** Bloqueo activo (devuelve error 403 Forbidden ante amenazas).

### 2. Reglas OWASP CRS (Core Rule Set)
Debido a inconsistencias en los paquetes de repositorios estándar, las reglas se han implementado clonando directamente el repositorio oficial de GitHub para asegurar la última versión y compatibilidad.
* **Fuente:** `https://github.com/coreruleset/coreruleset.git`
* **Integración:** Se cargan mediante `Include` en la configuración de Apache.

### 3. Correcciones de Entorno (Fixes)
Para adaptar la imagen base de Apache (`httpd`) al paquete de ModSecurity de Ubuntu, se realizaron ajustes manuales en el `Dockerfile`:
* Creación explícita de directorios de logs (`/var/log/apache2/`) para evitar el cierre inesperado del contenedor.
* Carga manual de dependencias (`unique_id_module`) requeridas por el WAF.

## 📸 Evidencia de Funcionamiento

Se ha realizado una prueba de concepto simulando un ataque de **Cross-Site Scripting (XSS)** reflejado mediante la URL:
`https://localhost:8081/?param=<script>alert(1)</script>`

**Resultado:**
El WAF intercepta la petición maliciosa y deniega el acceso inmediatamente.

![Evidencia de Bloqueo 403](Imagen_1.png)
*(El servidor devuelve "Forbidden" al detectar el patrón `<script>` en los parámetros).*

## 🚀 Despliegue

Para construir y ejecutar este contenedor:

```bash
# 1. Construir la imagen (asegúrate de tener la imagen base 'apache-hardening' creada)
docker build -t apache-waf .

# 2. Ejecutar el contenedor (Mapeando puerto 8081)
docker run -d -p 8081:443 --name mi-waf apache-waf
