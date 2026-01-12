# --- NIVEL 2: WAF (ModSecurity + OWASP CRS Git) ---
FROM apache-hardening

ARG DEBIAN_FRONTEND=noninteractive

# 1. Instalar Motor ModSecurity y GIT (para bajar las reglas)
RUN apt-get update && \
    apt-get install -y libapache2-mod-security2 libaprutil1-ldap git && \
    apt-get clean

# 2. Configurar el Motor (SecRuleEngine On)
RUN cp /etc/modsecurity/modsecurity.conf-recommended /etc/modsecurity/modsecurity.conf && \
    sed -i 's/SecRuleEngine DetectionOnly/SecRuleEngine On/' /etc/modsecurity/modsecurity.conf

# 3. DESCARGAR REGLAS OWASP (CRS) OFICIALES
#    Borramos cualquier carpeta previa y clonamos la versión oficial
RUN rm -rf /usr/share/modsecurity-crs && \
    git clone https://github.com/coreruleset/coreruleset.git /usr/share/modsecurity-crs

# 4. Configurar las reglas
#    Ahora sí existe el archivo de ejemplo porque lo acabamos de bajar de la fuente oficial
RUN cp /usr/share/modsecurity-crs/crs-setup.conf.example /usr/share/modsecurity-crs/crs-setup.conf

# 5. FIX DE LOGS (Vital para que no crashee)
RUN mkdir -p /var/log/apache2/ && \
    touch /var/log/apache2/modsec_audit.log && \
    chmod 777 /var/log/apache2/modsec_audit.log

# 6. INYECCIÓN EN HTTPD.CONF
RUN sed -i 's/#LoadModule unique_id_module/LoadModule unique_id_module/' /usr/local/apache2/conf/httpd.conf && \
    echo "" >> /usr/local/apache2/conf/httpd.conf && \
    echo "# --- WAF & OWASP CRS Config ---" >> /usr/local/apache2/conf/httpd.conf && \
    echo "LoadModule security2_module /usr/lib/apache2/modules/mod_security2.so" >> /usr/local/apache2/conf/httpd.conf && \
    echo "Include /etc/modsecurity/modsecurity.conf" >> /usr/local/apache2/conf/httpd.conf && \
    echo "Include /usr/share/modsecurity-crs/crs-setup.conf" >> /usr/local/apache2/conf/httpd.conf && \
    echo "Include /usr/share/modsecurity-crs/rules/*.conf" >> /usr/local/apache2/conf/httpd.conf

EXPOSE 80 443 8081
