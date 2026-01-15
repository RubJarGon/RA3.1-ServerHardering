FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive

# 1. Instalación de dependencias
RUN apt-get update && apt-get install -y \
    apache2 \
    libapache2-mod-security2 \
    wget \
    tar \
    && apt-get clean

# 2. Habilitar módulos
RUN a2enmod security2 headers

# 3. Configuración inicial de ModSecurity
RUN cp /etc/modsecurity/modsecurity.conf-recommended /etc/modsecurity/modsecurity.conf
RUN sed -i 's/SecRuleEngine DetectionOnly/SecRuleEngine On/' /etc/modsecurity/modsecurity.conf

# 4. Descargar OWASP CRS v3.3.5
WORKDIR /opt
RUN wget https://github.com/coreruleset/coreruleset/archive/refs/tags/v3.3.5.tar.gz
RUN tar -xzvf v3.3.5.tar.gz
RUN mv coreruleset-3.3.5 owasp-crs
RUN rm v3.3.5.tar.gz

# Configuración base de CRS
RUN cp /opt/owasp-crs/crs-setup.conf.example /opt/owasp-crs/crs-setup.conf

# --- SOLUCIÓN DEL ERROR ---
# Eliminamos ESPECÍFICAMENTE la regla 922 que causa la incompatibilidad de sintaxis
RUN rm /opt/owasp-crs/rules/REQUEST-922-MULTIPART-ATTACK.conf
# --------------------------

# 5. Configurar Apache
RUN printf "<IfModule security2_module>\n\
    SecDataDir /var/cache/modsecurity\n\
    IncludeOptional /etc/modsecurity/*.conf\n\
    Include /opt/owasp-crs/crs-setup.conf\n\
    Include /opt/owasp-crs/rules/*.conf\n\
</IfModule>" > /etc/apache2/mods-enabled/security2.conf

# Crear directorio de caché
RUN mkdir -p /var/cache/modsecurity && chown -R www-data:www-data /var/cache/modsecurity

EXPOSE 80

CMD ["apache2ctl", "-D", "FOREGROUND"]
