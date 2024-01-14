### On Single Server  
#### 1. Setting Complete Environment  
- Launch a server on any cloud provider.  
- In DigitalOcean, launched a server with the name myserver.  
![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/compose/screenshots/node.png)  
- Install Docker Engine on the server: [ Docker Engine ](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/swarm_handson.md#docker-engine-installation)
- Install Docker Compose using:  
```
> sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose #downloads the docker compose and places it in path mentioned

> sudo chmod +x /usr/local/bin/docker-compose #applies executable permissions
```
Based on the platforms docker compose should be installed: [ Docker Compose ](https://docs.docker.com/compose/install/)

#### 2. Creating Docker Compose File  
- Creating first docker compose file.  
```
> mkdir compose #create a directory

> cd compose #go to the directory

> vi docker-compose.yml #create a yml file and code

> Code: It starts two services nginx and redis.
    version: '3'
    services:
     webserver:
      image: nginx
     database:
      image: redis
```
- To validate the syntax and print the configuration:  
`docker-compose config`  

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/compose/screenshots/first_dc.png)

#### 3. Starting 
- To start the application:  
`docker-compose up -d`  
- This command reads the file, pulls the images, creates the containers, and starts them in the detached mode.    
![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/compose/screenshots/first_dc_up.png)  

#### 4. Container Status  
- To check the status of the running containers:  
`docker-compose ps`  

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/compose/screenshots/dc_ps.png)  

#### 5. Stopping  
- To stop the application:  
`docker-compose down`  
- This stops the complete apllication and removes the containers.  
![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/compose/screenshots/first_dc_down.png)  
