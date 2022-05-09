# 3-tier-application
This is a 3-tier application example which involves applications like posgres as database, redmine as app-server and nginx as web-server.

Here we are using docker-compose to deploy all the 3 applications.

This a docker-compose.yaml file

```
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
```

Before executing the docker-compose.yaml file, we need to setup the volume mount point.
Create the volume mount point - the script assume to have `/data` directory on the root, if it does not exists it will be created by the `setup` script.
```
  sudo ./setup
```

Start the application service using docker-compose:
```
  docker-compose up -d
```

Verify that the applicaiton service is up and running:
```
  docker ps
```
![Screen Shot 2022-05-07 at 4 09 46 PM](https://user-images.githubusercontent.com/35251635/167343745-40aff1e3-5b51-43d3-b254-c573b474ff03.png)

Access the Redmine appication using localhost and the port 8081 that we have expsoed in the docker-compose.yaml file

