FROM debian:bookworm-slim

ENV DEBIAN_FRONTEND=noninteractive

# 1. Instalación de paquetes
RUN apt-get update && apt-get install -y \
    nginx \
    libnginx-mod-http-modsecurity \
    php-fpm \
    openssl \
    apache2-utils \
    git \
    curl \
    ca-certificates \
    && apt-get clean

# 2. Certificados SSL
RUN mkdir -p /etc/nginx/ssl && \
    openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -keyout /etc/nginx/ssl/nginx.key -out /etc/nginx/ssl/nginx.crt \
    -subj "/C=ES/ST=Castellon/L=Castellon/O=IT/CN=localhost"

# 3. Directorio Protegido
RUN mkdir -p /var/www/html/privado && \
    echo "<h1>Area Privada Protegida</h1>" > /var/www/html/privado/index.html && \
    htpasswd -bc /etc/nginx/.htpasswd usuario password123

# 4. Configuración ModSecurity y OWASP
RUN mkdir -p /etc/nginx/modsec && \
    # Descargamos la config y el archivo de mapping que faltaba
    curl -o /etc/nginx/modsec/modsecurity.conf https://raw.githubusercontent.com/SpiderLabs/ModSecurity/v3/master/modsecurity.conf-recommended && \
    curl -o /etc/nginx/modsec/unicode.mapping https://raw.githubusercontent.com/SpiderLabs/ModSecurity/v3/master/unicode.mapping && \
    sed -i 's/SecRuleEngine DetectionOnly/SecRuleEngine On/' /etc/nginx/modsec/modsecurity.conf && \
    # Limpieza y descarga de reglas
    rm -rf /usr/share/modsecurity-crs && \
    git clone -b v3.3.5 https://github.com/coreruleset/coreruleset /usr/share/modsecurity-crs && \
    mv /usr/share/modsecurity-crs/crs-setup.conf.example /usr/share/modsecurity-crs/crs-setup.conf && \
    echo "Include /etc/nginx/modsec/modsecurity.conf" > /etc/nginx/modsec/main.conf && \
    echo "Include /usr/share/modsecurity-crs/crs-setup.conf" >> /etc/nginx/modsec/main.conf && \
    echo "Include /usr/share/modsecurity-crs/rules/*.conf" >> /etc/nginx/modsec/main.conf

# 5. Copiar archivos locales
COPY default.conf /etc/nginx/sites-available/default
COPY index.php /var/www/html/index.php

EXPOSE 80 443

# IMPORTANTE: Script de arranque
CMD service php8.2-fpm start && nginx -g "daemon off;"
