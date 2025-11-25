FROM ubuntu:20.04
RUN apt-get update
RUN apt-get install -y software-properties-common

RUN apt-get update

RUN apt-get install -y apache2

EXPOSE 80
CMD apachectl -DFOREGROUND
COPY index.html /var/www/html/
