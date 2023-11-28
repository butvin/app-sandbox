FROM httpd:2.4.57-alpine

RUN apk update;
RUN apk upgrade;

# Copy apache vhost file
COPY app.application.com.conf /usr/local/apache2/conf/app.application.com

RUN echo "Include /usr/local/apache2/conf/app.application.com" >> /usr/local/apache2/conf/httpd.conf