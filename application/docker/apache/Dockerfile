FROM httpd:2.4.54-alpine
LABEL maintainer="butvin"

RUN set -ex; \
  apk update; \
  apk upgrade;

# Copy apache vhost file
COPY application.conf /usr/local/apache2/conf/app.application.com

RUN echo "Include /usr/local/apache2/conf/app.application.com" >> /usr/local/apache2/conf/httpd.conf