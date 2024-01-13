#### Docker Swarm  
- It is a Docker's native container orchestration solution, which is used to manage and orchestrate the deployment of containers at scale.  
- Key features:  
    - Cluster management integrated with Docker Engine  
    - Decentralized Design  
    - Scaling  
    - Service Discovery  
    - Load Balancing  
    - Secure by default  
    - Rolling Updates  

#### Node  
- It is an instance of the Docker engine which is a part in the swarm cluster.  
- Nodes in a swarm can be physical machines, virtual machines, or cloud instances.  
- Two different types of nodes:  
    - Manager Node:  
        - To deploy an application to swarm, we should submit a service definition to the manager. 
        - They are responsible for managing and orchestrating the tasks in the cluster.  
        - Handles the scheduling of services.  
        - Must have atleast one manager node in the swarm cluster.  
        - Perform different actions like service deployment, scaling, rolling updates, and ensuring high availability.  
    - Worker Node:  
        - The manager node dispatches units of work called tasks to the worker nodes.  
        - Responsible for the running the tasks which are assigned by the manager nodes.  
        - Do not participate in the orchestration decisions.  
        - Can have any number of worker nodes.  

#### Service  
- It is a definition of the tasks to execute on the manager or the worker nodes.  
- Syntax:  
    `docker service create --name <service-name> --replicas <count> <image-name>`  
- Here, when we create a service, we specify which container image to use and which commands to execute inside running containers.  
- There are two types of services:  
    - Replicated Service:  
        - To run a specified number of replicas/tasks across the nodes in a swarm cluster.  
        - Swarm ensures that the specified number of replicas are running all the time.  
        - Syntax:  
        `docker service create --name <service-name> --replicas <count> <image-name>`  
    - Global Service:  
        - Useful for running the tasks that should be available on all the nodes.  
        - Like monitoring agents, or logging collectors.  
        - Syntax:  
        `docker service create --name <service-name> --mode global -dt <image-name>`  
- We can also publish a port to the service using:  
    `docker service create --name <service-name> --replicas <count> -p <host_port>:<container_port> <image-name>`  

#### Task and Container    
- A task represents a running instance of a container within a Docker Swarm service.  
- It carries a Docker container and the commands to run inside the container.  
- Once a task is assigned to a node, it cannot be moved to the another node - it should run or fail.  

#### Draining  
- Marking a node as unavailable.  
- Useful when there's a maintenance or decommissioning a node without affecting the running tasks.  
- When we drain a node the tasks that are running on that will be moved to the other available nodes in the cluster.  

#### Inspecting  
- Docker Inspect provides us detailed info about Docker objects.  
- In the docker swarm, inspect can be used to services and nodes.  
