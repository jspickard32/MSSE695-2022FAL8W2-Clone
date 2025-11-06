ARG dockerTag
FROM nextcloud:$dockerTag
COPY . /var/www/html/
RUN /bin/bash -c "/var/www/html/nextcloud-ssl-js.sh";
