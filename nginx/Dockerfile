FROM nginx:latest
RUN apt-get update -y && apt-get install -y vim curl sudo
#COPY nginx.conf /etc/nginx/conf.d/nginx.conf
#COPY ssl.conf /etc/nginx/conf.d/ssl.conf

COPY ./index.html /usr/share/nginx/html/index.html
COPY nginx.conf /etc/nginx/conf.d/default.conf

COPY ./ssl/server.crt /etc/nginx/ssl/server.crt
COPY ./ssl/server.key /etc/nginx/ssl/server.key
COPY ./ssl/server.password /etc/nginx/ssl/server.password

EXPOSE 80/tcp
EXPOSE 443/tcp
