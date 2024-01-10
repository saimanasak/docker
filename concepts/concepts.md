# CONCEPTS  
- [ Image ](#Image)
- [ Container ](#container)
- [ Registry ](#registry)
- [ Pushing an image ](#pushing-an-image)
- [ Tagging ](#tagging)
- [ Port Binding ](#port-binding)
- [ Daemon ](#daemon)
- [ Modes ](#modes)
- [ Default Container Commands ](#default-container-commands)
- [ Docker 'exec' ](#docker-exec)
- [ 'it' flags ](#it-flags)
- [ Restart Policies ](#restart-policies)
- [ Removing ](#removing)
- [ Commit ](#commit)
- [ Layers ](#layers)
- [ Pruning ](#pruning)
- [ Search ](#search)
- [ Moving images across hosts ](#moving-images-across-hosts)
- [ Build Cache ](#build-cache)
- [ Networking ](#networking)

<a name="Image"></a>
#### Image  
- It is a file that contains all the necessary dependencies and configurations which are required to run an application.

<a name="Container"></a>
#### Container
- It is a running instance of an image.
- A Universally Unique Identifier (UUID) when a container is created.
- By default, docker assigns a randomnly generated name containing of 2 words and combined by an underscore.
- We can also give a desired name to our container by  
    `
    --name=some_desired_container_name
    `

<a name="Registry"></a>
#### Registry  
- A stateless, highly scalable server side application and allows us to distribute docker images.  
- Can store and manage the docker images.  
- There are different registries available:  
    - Docker Hub  
    - Docker Trusted Registry  
    - Private Repository (like AWS ECR)  
    - Docker Registry  

<a name="Pushing an image"></a>
#### Pushing an image  
- Steps to push an image into registry URL:  
    - Login to the appropriate container registry using the command:   
        `docker login <registry_URL>`
    - It's a good practice to tag the image:  
        `docker tag <name_local_image> <registry_URL>/<repository_name>:<tag>`
    - Now, push the image to registry:  
        `docker push <registry_URL>/<repository_name>:<tag>`

<a name="Tagging"></a>
#### Tagging  
- A way to assign a name and a desired version to an image.  
- Syntax:  
    `docker build -t <repo_name>`  
    `docker tag <image_name>:<associated_tag> <new_image_name>:<new_tag>`
- Second syntax is used to create a tag to already existing image.
- If no tag is mentioned, it takes the default one i.e., **latest** tag.

<a name="Port Binding"></a>
#### Port Binding  
- By default, containers can communicate with the outside world but the outside world cannot communicate with the containers.
- So for this, we have a process called port binding.
- This maps a host port to the container port.
    - Host port: It is a port on the host machine to which docker binds and this allow external communication with the application/services running inside a container.
    - Container port: It is a port exposed by a container in which service/application is running and this listens for the incoming connections.  
- **-p** and **--publish** flags allow us to expose our desired ports from the docker container to the host machine. It publishes only the list of ports which are specified.  
Syntax:  
`docker run -dt --name <container_name> -p <host_port>:<container_port> <image_name>`  
`docker run -dt --name <container_name> --publish <host_port>:<container_port> <image_name>`
- **-P** is reffered to as publish all. All exposed ports are published to random port of a host.  
Syntax:  
`docker run -dt --name <container_name> -P <image_name>`  

<a name="Daemon"></a>
#### Daemon
- It manages the docker containers and is responsible for creating, running, and managing the docker containers on a host system.
- When we run a "docker run" command then docker CLI sends a command to the daemon and which then executes it.
- We can find the config file of docker daemon in the below path:  
    `
    /etc/docker/daemon.json
    `

<a name="Modes"></a>
#### Modes
- We have two modes: Attached/Foreground/Interactive and Detached modes.
    - Attached mode: 
        - Which means the container runs in the foreground. Here, the output can be seen on the terminal itself.
        - But, in this mode we can't get back the access to the terminal.
        - If we exit from here, then the running container gets stopped/terminated.
    - Detached Mode:
        - The container runs in the background and output can be seen on a different terminal.
        - Here, we get the access to the terminal.
        - We use **-d** option to enable this mode.

<a name="Default Container Commands"></a>
#### Default Container Commands
- When we start a container, a default command is executed that runs as PID1.
- This default command can be defined when we define a container image.
- Can override this command.
- Syntax:  
    `docker run -d <image_name> <command>`

<a name="Docker 'exec'"></a>
#### Docker 'exec'
- Docker container exec command is used to run a new command in a running container.
- This can be used only when the PID1 (i.e., the primary process) is running.
- If we stop the primary process then the container is stopped/terminated.
- This will not be restarted if the container is restarted.

<a name="'it' flags"></a>
#### 'it' flags
- Every process that is created in the linux environment has three file descriptors: stdin, stdout, and stderr.
- 'it flag'
    - **-i** is an interactive flag that keeps stdin open even if it's not attached (if not mentioned, we cannot give any input as we desired)
    - **-t** is a tty flag that allocates a pesudo-TTY (which means it gives us a terminal to work on)

<a name="Restart Policies"></a>
#### Restart Policies
- By default, containers will not start when they exit or when daemon is restarted.
- Docker provides few restart policies to decide whether the containers should start automatically when they exit or when the daemon restarts.
- Use **--restart** flag with the run command.  
- Options and their syntax:   
    - no restart policy(default): `docker run -d --name <container_name> <image_name>`   
    - always: Always restart the container if it stops. If it's manually stopped, it's restarted only when Docker daemon restarts or the container itself is manually restarted.
    `docker run -d --name <container_name> --restart always <image_name>`  
    - unless stopped (manually): Similar to always, except that when the container is stopped (manually or otherwise), it isn't restarted even after Docker daemon restarts.
    `docker run -d --name <container_name> --restart unless-stopped <image_name>`  
    - on failure (exits with a non-zero status): `docker run -d --name <container_name> --restart on-failure:<max_no.of_restart_attempts> <image_name>`  
- Policies can be applied or modified for the running container as well:  
    `docker update --restart option <container_name>`  
    
<a name="Removing"></a>
#### Removing
- To remove a running container, first we need to stop the container and then remove.
- We cannot directly remove a running container.
- If we use **--rm** flag then it automatically removes the container then it exits.  
- Syntax:  
    `docker stop <container_name>`  
    `docker rm <container_name>`  
- We can also remove the images, if there are no containers associated to it:  
    `docker rmi <image_name>`  
- We can also remove the images, if there are any containers associated to it:  
    `docker rmi -f <image_name>`  

<a name="Commit"></a>
#### Commit
- Creating a new image from a modified container.
- i.e., When we make few modifications in a running container and from this we can create a new image from the modified running container.  
- Syntax:  
`docker commit <running_container_name/id> <new_image_name>:<tag(optional)>`
- We can also change the CMD of the previous image during the commit.  
Example:  
`docker commit --change='CMD ["some_command"]' <running_container_name/id> <new_image_name>:<tag(optional)>`
- We can make changes to the following instructions - CMD, ENTRYPOINT, ENV, EXPOSE, LABEL, ONBUILD, USER, VOLUME, WORKDIR.

<a name="Layers"></a>
#### Layers  
- Each instruction in a dockerfile creates a new layer in the image.
- Readable and immutable.
- Once a layer is created, it cannot be modified.
- All the layers can be seen using -  
`docker inspect <image_name>`  
`docker history <image_name>`    
- All the changes made in the container will not be affected to the layer.  
- Flattening docker images:  
    - This helps to reduce the size of an image.  
    - More number of layers, more the size of an image.  
    - One way is:  
        - Exporting and Importing the container.  
        - Export: `docker export container_name > newfilename.tar`  
        - Import: `cat newfilename.tar | docker import - new_image_name:tag`    

<a name="Pruning"></a>
#### Pruning  
- Cleans up unused images.  
- By default, it cleans up dangling images (images without tags and images that aren't referenced by any container)
- Syntax:  
`docker image prune`  
`docker image prune -a`

<a name="Search"></a>
#### Search  
- It searches for images in Docker Hub.  
- Syntax:  
    `docker search <keyword>`  
- Options:  
    - --filter (based on STARS, IS-AUTOMATED, IS-OFFICIAL)  
    - --format  
    - --limit  
    - --no-trunc  

<a name="Moving images across hosts"></a>
#### Moving images across hosts  
- Steps:  
    - First we need to **save** one or more images to a tar archive.  
        `docker save <image_name> > file.tar`  
    - Then we need to **load** an image from a tar archive.  
        `docker load < file.tar`  

<a name="Build Cache"></a>
#### Build Cache  
- Docker creates the images using layers.  
- Each instruction in a dockerfile creates a new layer in the image.  
- Docker uses a layer cache to optimize the process of building the docker images and make it faster.  
- If cache can't be used for any particular layer, then all the subsequent layers won't be loaded from the cache.  

<a name="Networking"></a>
#### Networking  
- Using this containers can communicate among them or with internet or be isolated.  
- Using drivers, docker network subsystem is pluggable.  
- Different available drivers are:  
    - Bridge  
    - Host  
    - None  
    - Overlay  
    - Macvlan  
- Default driver is Bridge.  
- Bridge Network:  
    - It allows the containers connected to the same bridge network to communicate among them and the containers will be isolated from the other containers that aren't part of bridge network.  
    - Containers communicate with each other using the ip-address.
    - Example:  
      ```
      #create two containers  
      > docker run -d --name mynginx01 nginx  
      > docker run -d --name mynginx02 nginx  

      #inspect one of the containers to know the ip-address
      > docker inspect mynginx01  

      #login to one of the containers and connect to the other container  
      > docker container exec -it mynginx02 bash  
      > ping <ipaddress_of_mynginx01>
      ```
- User-defined Bridge Network:  
    - This is superior to bridge network.  
    - There are many differences between the two.  
    - Here, containers communicate with each other using the name of the container.  
    - Example:  
      ```
      #create a bridge with a name mybridge
      docker network create --driver bridge mybridge

      #create two containers  
      > docker run -d --name mynginx01 --network mybridge nginx  
      > docker -d --name mynginx02 --network mybridge nginx  

      #login to one of the containers and connect to the other container  
      > docker container exec -it mynginx02 bash  
      > ping mynginx01
      ```
    - Using below command we can know the info about the bridge interfaces on the system which includes names of bridges, associated interfaces, and their states.  
        `brctl show`  
- Host Network:  
    - Here, container directly uses the network of the host system i.e., it gets full access of the host's network interfaces.  
    - Containers running in host network mode can access the same network interfaces, ports, and the  ip-addresses as processes running on the host.  
    - Syntax:  
        `docker run -d --name <container_name> --network host <image_name>`  
    - When two different applications like nginx and tomcat are running in the host network mode, then each of the application will be accessed using their ports i.e., for nginx on port 80 and for tomcat on port 8080.   
- None Network:  
    - Using this we can disable the networking stack on a container.  
    - No ip will be configured, no access to the external networks, and no communication can be made with the other containers.  
- Legacy approach for linking the containers:  
    `docker run -d --link <source_container>:<alias_name> --name <container_name> <image_name>`  
