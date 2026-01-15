FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive

# 1. Instalar Apache
RUN apt-get update && apt-get install -y \
    apache2 \
    curl \
    && apt-get clean

# 2. HARDENING: Ocultar Versión del Servidor (ServerTokens/Signature)
# Esto evita que Apache diga "Soy Apache 2.4.52 en Ubuntu" en las cabeceras.
RUN echo "ServerTokens Prod" >> /etc/apache2/apache2.conf && \
    echo "ServerSignature Off" >> /etc/apache2/apache2.conf

# 3. HARDENING: Deshabilitar listado de directorios
# Buscamos donde dice "Options Indexes" y lo quitamos para que no se listen los archivos.
RUN sed -i 's/Options Indexes FollowSymLinks/Options FollowSymLinks/' /etc/apache2/apache2.conf

# 4. HARDENING: Cabeceras de Seguridad (Security Headers)
# Habilitamos el módulo de cabeceras
RUN a2enmod headers

# Inyectamos las cabeceras recomendadas por Geekflare/OWASP
RUN echo "\n<IfModule mod_headers.c>" >> /etc/apache2/apache2.conf && \
    echo "    # Evitar Clickjacking" >> /etc/apache2/apache2.conf && \
    echo "    Header set X-Frame-Options \"SAMEORIGIN\"" >> /etc/apache2/apache2.conf && \
    echo "    # Protección contra XSS" >> /etc/apache2/apache2.conf && \
    echo "    Header set X-XSS-Protection \"1; mode=block\"" >> /etc/apache2/apache2.conf && \
    echo "    # Evitar MIME Sniffing" >> /etc/apache2/apache2.conf && \
    echo "    Header set X-Content-Type-Options \"nosniff\"" >> /etc/apache2/apache2.conf && \
    echo "</IfModule>" >> /etc/apache2/apache2.conf

# 5. Crear página de prueba
RUN echo "<h1>Apache Hardened Ready!</h1>" > /var/www/html/index.html

EXPOSE 80

CMD ["apachectl", "-D", "FOREGROUND"]
