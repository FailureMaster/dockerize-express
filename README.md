

### Create a dockerfile:
```
FROM node:latest
RUN mkdir -p /var/www/app
WORKDIR /var/www/app
COPY package*.json ./
RUN npm install && npm cache clean --force
ENV PATH=/var/www/app/node_modules/.bin:$PATH
RUN mkdir -p /var/www/app/src
WORKDIR /var/www/app/src
COPY src .
```

Create a docker-compose.yml
```
version: "3.2"

services:

    db:

      image: mysql:5.7 # instructs Docker that the db service is going to use a MySQL image version 5.7

      volumes:

        # db_data is a name we set for the /var/lib/mysql path (We can change db_data to whatever)

        # that we can then use to share data between two or more services.

        - db_data:/var/lib/mysql

        # this is a way for us to auto IMPORT a .sql file to the database in your MySQL container

        # it auto imports the db.sql file in the mysql-dump folder

        - ./mysql-dump:/docker-entrypoint-initdb.d

      restart: always

      environment: # It creates the MySQL credentials for us, as well as a database called hh. This is the credential you'll need in order for your express app to communicate with the MySQL service.

        MYSQL_ROOT_PASSWORD: password

        MYSQL_DATABASE: hh

      ports:

        # Private port for MySQL is 3306 and Public port is 3307. To connect to our MySQL container using Workbench, we use port 3307

        - 3307:3306

  

    web_app:

      depends_on: # This will tell Docker to start services in dependency order. In this example, before starting the app service, it waits for db to be started.

        - db

      build: ./

      volumes:

        # This maps our host project files to our container's WORKDIR. Whatever changes we make to our local copy, will reflect those changes in WORKDIR.

        - ./src:/var/www/app/src

        - ./package.json:/var/www/app/package.json

      command: npm start

      restart: always

      ports:

        - 3001:3000

  

volumes: # it means that that specified volume (in this case, db_data) is available to all services (both app and db).

    db_data: {}
```

### Use visual studio code and do the compose up

![Pasted image 20240328200557](https://github.com/FailureMaster/dockerize-express/assets/54210252/b5828c46-facf-431d-b0a5-92c73b74ef32)


### Go to the docker tab and open in browser:

![Pasted image 20240328200629](https://github.com/FailureMaster/dockerize-express/assets/54210252/5ce7e2a8-211d-4679-aa17-1b85f35112de)


### You should see this: 
![Pasted image 20240328200837](https://github.com/FailureMaster/dockerize-express/assets/54210252/3616888c-229d-4b26-b06d-05641e160c49)
