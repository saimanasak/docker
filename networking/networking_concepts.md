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
