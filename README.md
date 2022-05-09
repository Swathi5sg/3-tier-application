# 3-tier-application
This is example for 3-tier application which involves applications like posgres as database, redmine as app-server and nginx as web-server.

Here we are using docker-compose to deploy all the 3 applications.

This a docker-compose.yaml file

version: "3"
networks:
  net:
    driver: bridge  
services:  
  db_server:
    restart: on-failure
    image: f2hex/postgres
    container_name: redmine-db_server
    deploy:
       resources:
          limits:
             cpus: '1'
             memory: 256M
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=postgres
    ports:
      - "5432:5432"
    networks:
      - net
    volumes:
      - /data/redmine/db:/var/lib/postgresql/data
   

  app_server:
    restart: on-failure
    image: redmine
    container_name: redmine-app_server
    deploy:
       resources:
          limits:
             cpus: '1'
             memory: 256M
    ports:
      - "8081:3000"
    networks:
      - net
    volumes:
      - /data/redmine/app/files:/usr/src/redmine/files
    

  web_server:
    restart: on-failure
    image: nginx
    container_name: redmine-web_server
    deploy:
       resources:
          limits:
             cpus: '1'
             memory: 256M
    ports:
      - "8000:80"
    networks:
      - net
    volumes:
      - /data/redmine/web/log:/var/log/nginx





