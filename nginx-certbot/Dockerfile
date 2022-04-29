FROM nginx

COPY ./nginx/conf/ /etc/nginx/conf.d/
COPY ./certbot/www /var/www/certbot/
COPY ./certbot/conf/ /etc/nginx/ssl/
COPY ./nginx/conf /etc/nginx/conf.d/

EXPOSE 443
EXPOSE 80
