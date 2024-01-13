#### Setting up an environment  
- Launch the servers in any of the cloud provider.  
- Selected DigitalOcean provider.  
- Launched three nodes with names: swarm-1, swarm-2, and swarm-3 nodes.  
![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/nodes.png)

#### Install docker engine  
- Install docker engine on all the nodes.    
- Installation steps for centOS (Here, centOS platform servers were launched):  
    ```
    sudo yum install -y yum-utils #yum-utils package will be installed  

    sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo #setting up the repo  

    sudo yum install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin #docker installation  

    sudo systemctl start docker #starting docker  
    ```  
- For other platforms, link to install docker engine --> [Docker Engine](https://docs.docker.com/engine/install/)  
- Command to check if docker is running on server:  
`systemctl status docker`  

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/docker_status.png)

#### Creating a swarm cluster  
- To form a cluster with the 3 nodes: make one of them as manager and remaining 2 nodes as worker nodes.  
- swarm-1: Manager Node  
  swarm-2 and swarm-3: Worker Nodes  
- Command to create a manager node:  
    `docker swarm init --advertise-addr <manager_node_ip>` 

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/manager_node1.png)  

- To get the IP address of the node:  
    - Directly use the public IP address (i.e., IPv4) of the server.  
    - Using `ifconfig` command. 
        - To use the above command, install **net-tools** package using:  
            `yum install net-tools`  
        - In the ifconfig, can find the IP address in the **eth0** option:  
        ![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/ifconfig.png)
            
- Command to create a worker node:  
    `docker swarm join --token ......`  
    - Copy the token command and run it in the worker nodes of the cluster.  
    ![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/worker_node2.png)  
  
    ![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/worker_node3.png)  

- Command to list the nodes in a cluster:  
`docker node ls`  
    - Leader is the manager node and remaining nodes are the worker nodes. So, all the nodes in the cluster are up and running.   
![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/docker_node.png)  

#### Creating a service  
- Command to create a service:  
`docker service create --name <service-name> --replicas <count> <image-name>`
- Created a service called **mynginx** using **nginx** image with 2 tasks (i.e., containers)  
- Command: `docker service create --name mynginx --replicas 2 nginx`  

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/service_create.png)

#### Listing services  
- Syntax to list services that are running in the swarm.  
`docker service ls`  
- So, mynginx is the service that is running in the cluster.

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/service_list.png)  

#### Listing the tasks  
- Syntax to list the tasks on the running instances which are associated with a specific service.  
`docker service ps <service-name>`  
- There are 2 tasks running in the cluster.  
- Command used: `docker service ps mynginx`  

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/service_ps.png)  

#### Scaling  
- Can scale the tasks by using two ways.
    1. Using **scale** option  
        - Syntax: `docker service scale <service-name>:<count>`  
        - To scale up the tasks by 5, command used:  
            `docker service scale mynginx=5`  
        
        ![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/scale_up.png)  
