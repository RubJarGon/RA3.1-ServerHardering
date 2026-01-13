FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive

# 1. Instalar Apache y OpenSSL
RUN apt-get update && apt-get install -y \
    apache2 \
    openssl \
    && apt-get clean

# 2. Generar Certificado SSL Autofirmado
# Creamos la clave privada (.key) y el certificado público (.crt)
RUN openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -keyout /etc/ssl/private/apache-selfsigned.key \
    -out /etc/ssl/certs/apache-selfsigned.crt \
    -subj "/C=ES/ST=Castellon/L=Castellon/O=IES/CN=localhost"

# 3. Configurar Apache para usar SSL
# Habilitamos el módulo SSL
RUN a2enmod ssl

# Modificamos la configuración por defecto de SSL para que use nuestros certificados
RUN sed -i 's|/etc/ssl/certs/ssl-cert-snakeoil.pem|/etc/ssl/certs/apache-selfsigned.crt|g' /etc/apache2/sites-available/default-ssl.conf && \
    sed -i 's|/etc/ssl/private/ssl-cert-snakeoil.key|/etc/ssl/private/apache-selfsigned.key|g' /etc/apache2/sites-available/default-ssl.conf

# Habilitamos el sitio SSL por defecto
RUN a2ensite default-ssl

# 4. Crear una página de prueba
RUN echo "<h1>Servidor Apache Seguro (HTTPS) funcionando!</h1>" > /var/www/html/index.html

# Exponemos el puerto estándar HTTPS
EXPOSE 443

# Arrancamos Apache en primer plano
CMD ["apachectl", "-D", "FOREGROUND"]
