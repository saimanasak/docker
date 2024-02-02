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

#### Locking  
- Locking a swarm cluster means restricting access to the cluster.  
- Swarm cluster contains a lot of sensitive info like TLS key (which is used to encrypt) and keys which are used to encrypt or decrypt the Raft logs on disk.  
- For security reasons or to prevent unauthorized changes locking can be used.  

#### Pending State  
- When a service is in the pending state, it means that it isn't able to start or facing issues int eh deployments.  
- There are many reasons for this case:  
    - Node Availability
    - Insufficient Resources  
    - Constraints or Placement Issues  
    - Configuration Errors  

#### Control Service Placement  
- While deploying the services, can control the placement of services using various options.  
- Some of them are:  
    - Node Constraints  
    - Placement Preferences  
    - Global and Replicated Services  
    - Resource Constraints  
    - Placement Constraints  

#### Creating Service using Templates  
- Syntax:  
    `docker service create --name <service-name> --<flag>="{{.placeholder}}-{{.another-key}} <image-name>`  
- Three supported flags:  
    - --hostname  
    - --mount  
    - --env  
- Valid Placeholders:  
    - Service.ID  
    - .Service.Name  
    - .Service.Labels  
    - .Node.ID  
    - .Node.Hostname  
    - .Task.ID  
    - .Task.Name  
    - .Task.Slot  

#### Split Brain Problem  
- In a distributed system, where the nodes are divided into separate groups, and each group believes it is the only active part of the system.  
- It occurs when nodes lose connectivity but continue to operate independently making decisions without the awareness of the state of other nodes.  
- Leads to inconsistencies, conflicts, and potential data corruption.  
- Network partitions, hardware failures, or software issues can lead to communication breakdowns between nodes, and then the nodes operate independently.  

#### Quorum  
- Used to ensure the reliablity and consistency of decisions made by a group of nodes or replicas.  
- It refers to the minimum number of nodes that should agree on a particular decision or action for it to be considered valid.  
- Election process.  
- We should maintain odd no.of nodes within the cluster.  
    - Cluster Size: No.of nodes in a cluster.  
    - Majority: Max no.of nodes needed to be available to maintain the cluster.  
    - Fault Tolerance: No.of that can be down in the cluster.  

| Cluster Size | Majority | Fault Tolerance |  
|--------------|----------|-----------------|  
| 1 | 0 | 0 |  
| 2 | 2 | 0 |  
| 3 | 2 | 1 |  
| 4 | 3 | 1 |  
| 5 | 3 | 2 |  
| 6 | 4 | 2 |  
| 7 | 4 | 3 |  

- High Availability of Manager nodes in a swarm cluster:  
    - It is good to have odd number of nodes in a cluster.  
    - Using a raft implementation, the managers maintain a consistent internal state of the entire swarm and all the services that are running on it.  
    - A **N** manager cluster tolerates the loss of atmost **(N-1)/2** managers.  
- Syntax to recover the nodes from losing the Quorum:  
    `docker swarm init --force-new-cluster --advertise-addr <ip-address>`  

#### Routing Mesh  
- It enables each node in the swarm to accept connections on the published ports for any service running in the swarm, even if there's no task running on the node.  
- i.e., it allows a service to be accessed on any node in the swarm, regardless of where the service tasks are running.  

#### Secrets  
- Docker secrets to centrally manage this data and securely transmit it to only those containers that have access to it.  
- Secrets are encrypted during transit and at rest in swarm.  
- A secret is only accessible to the services which have been granted the explicit access to it, and only while those service tasks are running.  
- Syntax to create a secret:  
    `docker secret create <secret-name> <secret-file-name>`  

#### MTLS
- Mutual Transport Layer Security is a security mechanism where both server and the client in a communication exchange authenticate each other.  
- It is used to authenticate, authorize, and encrypt the communication with the other nodes in a swarm.  
- All the certificates are stored in the path:  `/var/lib/docker/swarm/certificates`  
- By default, manager node generates a new root Certificate Authority (CA) along with a key pair, which are used to secure communications with the other nodes that join the swarm.  
- Can give own externally generated root CA, using `--external-ca` flag along with the swarm init command.  
- Whenever a node joins the swarm, the manager provides a certificate to the node.  
- There will be a secure communication among the managers, and the workers using the digital certificates.  
- Generally, manager generates two tokens - one for manager and the other for the worker.  
- Each token includes the digest of the root CA's certificate and a randomly generated secret.  
- Whenever a node joins a swarm, the node which is joining uses the digest to validate the root CA certificate from the remote manager.  
- Can generate a new certificate using - `docker swarm ca --rotate`  
    - The above one is generated cross-signed certificate signed by the previous old CA.  

#### Docker Content Trust  
- It is a security feature that provides a way to verify the integrity and authenticity of the images.  
- Helps to ensure that the images we use are from a trusted source and haven't been tampered.  
- Command to enable Docker Content Trust:  
    `export DOCKER_CONTENT_TRUST=1`  
