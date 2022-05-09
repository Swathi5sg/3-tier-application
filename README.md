# 3-tier-application
This is example for 3-tier application which involves applications like posgres as database, redmine as app-server and nginx as web-server.

Here we are using docker-compose to deploy all the 3 applications.

This a docker-compose.yaml file

Creating docker-compose.yml
version: '2'
networks:
  net:
     driver: bridge
services:
  php:
     networks:
       - net
     image: phpmyadmin/phpmyadmin
     deploy:
       resources:
         limits:
           cpus: '0.5'
           memory: 50M
     container_name: php
     restart: on-failure
     links:
       - mysql:db
     depends_on:
       - mysql

  mysql:
    networks:
      - net
    image: k0st/alpine-mariadb
    deploy:
      resources:
        limits:
          cpus: '0.5'
          memory: 50M
    container_name: mysql
    restart: on-failure
    volumes:
      - ./data/mysql:/var/lib/mysql
    environment:
      - MYSQL_DATABASE=mydb
      - MYSQL_USER=myuser
      - MYSQL_PASSWORD=mypass

  nginx:
    networks:
      - net
    image: nginx:stable-alpine
    deploy:
      resources:
        limits:
          cpus: '0.5'
          memory: 50M
    container_name: nginx
    restart: on-failure
    ports:
      - "81:80"
    volumes:
      - ./nginx/log:/var/log/nginx
      - ./nginx.conf:/etc/nginx/nginx.conf:ro
    depends_on:
      - php






