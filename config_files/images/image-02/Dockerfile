FROM debian

LABEL description="Apache2"
LABEL version="1.0.0"

ENV APACHE_LOCK_DIR="/var/lock"
ENV APACHE_PID_FILE="/var/run/apache2.pid"
ENV APACHE_RUN_USER="www-data"
ENV APACHE_RUN_GROUP="www-data"
ENV APACHE_LOG_DIR="/var/log/apache2"

RUN apt-get update && apt-get install -y apache2 && apt-get clean
RUN echo "ServerName localhost" >> /etc/apache2/apache2.conf

VOLUME /var/www/html
EXPOSE 80

ENTRYPOINT ["/usr/sbin/apachectl", "-D", "FOREGROUND"]
