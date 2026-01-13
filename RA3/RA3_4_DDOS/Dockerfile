FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive

# 1. Instalar Apache y mod_evasive
RUN apt-get update && apt-get install -y \
    apache2 \
    libapache2-mod-evasive \
    && apt-get clean

# 2. Habilitar el módulo
RUN a2enmod evasive

# 3. Crear directorio de logs
RUN mkdir -p /var/log/mod_evasive && \
    chown -R www-data:www-data /var/log/mod_evasive

# 4. Configuración AGRESIVA (Directo a mods-available)
RUN printf "<IfModule mod_evasive20.c>\n\
    DOSHashTableSize    3097\n\
    DOSPageCount        2\n\
    DOSSiteCount        50\n\
    DOSPageInterval     1\n\
    DOSSiteInterval     1\n\
    DOSBlockingPeriod   10\n\
    DOSLogDir           \"/var/log/mod_evasive\"\n\
</IfModule>" > /etc/apache2/mods-available/evasive.conf

EXPOSE 80
CMD ["apache2ctl", "-D", "FOREGROUND"]
