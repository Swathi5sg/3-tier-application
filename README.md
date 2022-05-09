# 3-tier-application
This is a 3-tier application example which involves applications like postgres as database, redmine as app-server and nginx as web-server.

Here we are using docker-compose to deploy all the 3 applications.

This a docker-compose.yaml file

```
version: "3"
networks:
  net:
    driver: bridge  
services:  
  postgres:
    networks:
      - net
    restart: on-failure
    image: f2hex/postgres
    container_name: postgres
    deploy:
       resources:
          limits:
             cpus: '1'
             memory: 256M
    environment:
      - POSTGRES_USER=redmine
      - POSTGRES_PASSWORD=secret
    volumes:
      - /data/redmine/db:/var/lib/postgresql/data
   

  redmine:
    networks:
      - net
    restart: on-failure
    image: redmine
    container_name: redmine
    deploy:
       resources:
          limits:
             cpus: '1'
             memory: 256M
    links:
      - postgres:db
    volumes:
      - /data/redmine/app/files:/usr/src/redmine/files
    depends_on:
      - postgres 
    

  nginx:
    networks:
      - net
    restart: on-failure
    image: nginx
    container_name: nginx
    deploy:
       resources:
          limits:
             cpus: '1'
             memory: 256M
    ports:
      - "81:80"
    volumes:
      - /data/redmine/web/log:/var/log/nginx
      - ./nginx.conf:/etc/nginx/nginx.conf:ro 
    depends_on:
      - redmine
```
We should also create nginx.conf file, which act as revere-proxy for the redmine app-server

This is nginx.conf file

```
worker_processes 1;
events {
 worker_connections 32;
}
http {
 sendfile off;
 server {
 listen 80;
 location / {
 proxy_pass http://redmine.net;
 proxy_set_header Host $host;
 proxy_redirect off;
 }
 }
}
```

Before executing the docker-compose.yaml file, we need to setup the volume mount point.
Create the volume mount point - the script assume to have `/data` directory on the root, if it does not exists it will be created by the `setup` script.
```
  sudo ./setup
```

Deploy the application service using docker-compose:
```
  docker-compose up -d
```

Verify that the applicaiton service is up and running:
```
  docker ps
```
![Screen Shot 2022-05-09 at 5 35 33 PM](https://user-images.githubusercontent.com/35251635/167407093-bc4c030c-57aa-453b-8563-7b89777d9bbd.png)


Access the Redmine appication using localhost and the port 81 that we have expsoed in the docker-compose.yaml file

Here it is redirecting to Planio web page which is commercially hosted version of Redmine

![Screen Shot 2022-05-09 at 5 39 30 PM](https://user-images.githubusercontent.com/35251635/167407205-d53a0e4e-49ab-4f6a-a53e-3d4180a6840d.png)

Clicking on the planio web-page would take you to the exact page of Palnio

![Screen Shot 2022-05-09 at 5 39 42 PM](https://user-images.githubusercontent.com/35251635/167407241-1ec6ab11-dc72-4d36-9b33-6609175ddebe.png)




