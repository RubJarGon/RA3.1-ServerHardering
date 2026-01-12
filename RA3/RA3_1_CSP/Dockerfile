FROM httpd:2.4

# 1. Habilitar módulos necesarios (Headers y SSL)
RUN sed -i 's/#LoadModule headers_module/LoadModule headers_module/' /usr/local/apache2/conf/httpd.conf \
    && sed -i 's/#LoadModule ssl_module/LoadModule ssl_module/' /usr/local/apache2/conf/httpd.conf \
    && sed -i 's/#LoadModule socache_shmcb_module/LoadModule socache_shmcb_module/' /usr/local/apache2/conf/httpd.conf \
    && sed -i 's/#Include conf\/extra\/httpd-ssl.conf/Include conf\/extra\/httpd-ssl.conf/' /usr/local/apache2/conf/httpd.conf

# 2. Deshabilitar el módulo autoindex (Hardening) [cite: 20]
RUN sed -i 's/LoadModule autoindex_module/#LoadModule autoindex_module/' /usr/local/apache2/conf/httpd.conf

# 3. Copiar certificados y configurar cabeceras de seguridad
COPY ./server.crt /usr/local/apache2/conf/server.crt
COPY ./server.key /usr/local/apache2/conf/server.key

# Inyectar HSTS y CSP en la configuración 
RUN echo "Header always set Strict-Transport-Security \"max-age=63072000; includeSubDomains\"" >> /usr/local/apache2/conf/httpd.conf \
    && echo "Header set Content-Security-Policy \"default-src 'self';\"" >> /usr/local/apache2/conf/httpd.conf

COPY ./index.html /usr/local/apache2/htdocs/
