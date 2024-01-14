- [ On Single Server ](#on-single-server)
    - [ Setting Complete Environment ](#1-setting-complete-environment)
    - [ Creating Docker Compose File ](#2-creating-docker-compose-file)

<a name="first"></a>
### On Single Server  

<a name="one"></a>  
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

<a name="two"></a>  
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

<a name="three"></a>
#### 3. Starting 
- To start the application:  
`docker-compose -f <filename> up -d`  
- This command reads the file, pulls the images, creates the containers, and starts them in the detached mode.    

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/compose/screenshots/first_dc_up.png)  

<a name="four"></a>
#### 4. Container Status  
- To check the status of the running containers:  
`docker-compose ps`  

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/compose/screenshots/dc_ps.png)  

<a name="five"></a>
#### 5. Stopping  
- To stop the application:  
`docker-compose -f <filename> down`  
- This stops the complete apllication and removes the containers.  

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/compose/screenshots/first_dc_down.png)  

<a name="second"></a>
### In Swarm Cluster 

#### 1. Setting Complete Environment  
- Launching the servers: [ Nodes ](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/swarm_handson.md#setting-up-an-environment)  

- Install docker engine on all the nodes in the cluster:  [ Docker Engine ](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/swarm_handson.md#docker-engine-installation)  

- Install docker compose:  

- Forming a cluster: [ Swarm Cluster ](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/swarm_handson.md#creating-swarm-cluster)  

- Creating a docker compose file:  

#### 2. Stack Deploy  
- Syntax to deploy an application in the swarm cluster:  
`docker stack deploy --compose-file <filename> <stack-name>`  
- Command used to deploy a sample .yml file with the above configuartion:  
`docker stack deploy --compose-file docker-compose.yml myfile`  

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/compose/screenshots/stack_deploy.png)  

#### 3. Listing Tasks
- Syntax to list all the tasks running in a specific stack:  
`docker stack ps <stack-name>`  

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/compose/screenshots/stack_ps.png)  

#### 4. Listing Stacks  
- Syntax to list all the stacks present in the swarm cluster:  
`docker stack ls`  

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/compose/screenshots/stack_ls.png)  

#### 5. Removing Stack  
- Syntax to remove a stack:  
`docker stack rm <stack-name>`  
- Can remove the stack directly even if there are any running containers.  

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/compose/screenshots/stack_rm.png)  

> [!NOTE]  
> All the above commands that manage the cluster should be done only in the **manager** node.  