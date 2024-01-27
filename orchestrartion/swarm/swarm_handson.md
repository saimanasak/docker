# DOCKER SWARM  
- [ Setting up an Environment ](#setting-up-an-environment)
- [ Docker Engine Installation ](#docker-engine-installation)
- [ Creating Swarm Cluster ](#creating-swarm-cluster)
- [ Creating Service ](#creating-service)
- [ Listing Services ](#listing-services)
- [ Listing Tasks ](#listing-tasks)
- [ Scaling ](#scaling)
- [ Global Service ](#global-service)
- [ Draing Node ](#draining-node)
- [ Inspecting Service ](#inspecting-service)
- [ Inspecting Node ](#inspecting-node)
- [ Publishing Port ](#publishing-port)
- [ Removing Service ](#removing-service)  
- [ Removing Node ](#removing-node)  
- [ View Logs ](#view-logs)
- [ Locking ](#locking)
- [ Binding Volumes ](#binding-volumes)  
- [ Control Service Placement ](#control-service-placement)  
- [ Overlay Network ](#overlay-network)
- [ Creating Services using Templates ](#creating-services-using-templates)
- [ Routing Mesh ](#routing-mesh)
- [ Secrets ](#secrets)
- [ Leaving From Cluster ](#leaving-from-cluster)  
- [ MTLS ](#mtls)

<a name="setting"></a>
### Setting up an Environment  
- Launch the servers in any of the cloud provider.  
- Selected DigitalOcean provider.  
- Launched three nodes with names: swarm-1, swarm-2, and swarm-3 nodes.  
![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/nodes.png)

<a name="install"></a>
### Docker Engine Installation 
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

<a name="cluster"></a>
### Creating Swarm Cluster  
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

<a name="create"></a>
### Creating Service  
- Command to create a service:  
`docker service create --name <service-name> --replicas <count> <image-name>`
- Created a service called **mynginx** using **nginx** image with 2 tasks (i.e., containers)  
- Command: `docker service create --name mynginx --replicas 2 nginx`  

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/service_create.png)

<a name="listing services"></a>
### Listing Services  
- Syntax to list services that are running in the swarm.  
`docker service ls`  
- So, mynginx is the service that is running in the cluster.

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/service_list.png)  

<a name="listing tasks"></a>
### Listing Tasks  
- Syntax to list the tasks on the running instances which are associated with a specific service.  
`docker service ps <service-name>`  
- There are 2 tasks running in the cluster.  
- Command used: `docker service ps mynginx`  

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/service_ps.png)  

<a name="scaling"></a>
### Scaling  
- Can scale the tasks by using two ways.
    1. Using **scale** option  
        - Syntax: `docker service scale <service-name>=<count>`  
        - Using this way, we can scale the tasks to multiple services at once.  
            `docker service scale <service-name1>=<count1> <service-name2>=<count2>`  
        - To scale up the tasks by 5, command used:  
            `docker service scale mynginx=5`  
        
        ![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/scale_up.png)  

        - To scale down the tasks to 2, command used:  
            `docker service scale mynginx=2`  
        
        ![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/scale_down.png)
        
    2. Using **--update** option  
        - Syntax: `docker service update --replicas <count> <service-name>`  
        - Using this way, we scale the tasks to only one service at once.   
        - To scale up the tasks by 5, command used:  
            `docker service update --replicas 5 mynginx`  
        
        ![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/update_up.png)  

        - To scale down the tasks to 2, command used:  
            `docker service update --replicas=2 mynginx`  

        ![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/update_down.png)  

<a name="global"></a>
### Global Service  
- Synatx to create a global service:  
`docker service create --name <service-name> --mode global -dt <image-name>`  
- Using this we can run a service on all the nodes in the cluster.  
- Command used: `docker service create --name mybusybox --mode global -dt busybox`  

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/global_service.png)  

<a name="drain"></a>
### Draining Node  
- Synatx to make a node unavailable:  
    `docker node update --availability drain <node-name/id>`  
- Command used to drain swarm-2 node:  
    `docker node update --availability drain swarm-2`  

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/drain_node.png)
![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/nodes_after_drain.png)

- Now, the task mynginx which is running on swarm-2 has moved to swarm-3 node.  

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/swarm-2_before.png)  
![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/swarm-2_after.png)  
![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/swarm-3_after.png)  

- Command to make the drained node available:  
`docker node update --availability active <node-name/id>`  

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/nodes_after_active.png)  

<a name="inspect service"></a>
### Inspecting Service  
- Syntax to inspect a service: `docker service inspect <service-name/id> --pretty`  

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/inspect_service.png)  

<a name="inspect node"></a>
### Inspecting Node  
- Syntax: `docker node inspect <node-name/id> --pretty`  

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/inspect_node.png)  

<a name="publish"></a>
### Publishing Port  
- Syntax to publish a port to a service:  
    `docker service create --name <service-name> --replicas <count> -p <host_port>:<container_port> <image-name>`  
- Now, nginx will be running on port 8080 for all the containers/tasks running under the mynginx service.  
- Command: `docker service create --name mynginx --replicas 2 -p 8080:80 nginx`  

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/publish.png)  
- Can check the ports that are listening using: `netstat -ntlp`  

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/netstat.png)  

<a name="remove"></a>
### Removing Service  
- Syntax: `docker service rm <service-name/id>`  

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/remove.png)  

<a name="remove node"></a>
### Removing Node  
- Syntax to remove a node from the cluster:  
`docker node rm <node-name/id>`  
- Before removing the node, there should be no running containers/tasks in the node.  

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/remove_node.png)  

<a name="logs"></a>
### View Logs  
- Syntax to view logs of a service:  
`docker service logs <service-name/id>`  

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/logs.png)  

<a name="locking"></a>
### Locking  
- Syntax to lock or unlock a swarm cluster:  
`docker swarm update --autolock=true/false`  

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/locked_true.png)  

- Restart docker to check if lock fucntionality is enabled (testing purpose)  
Command: `systemctl restart docker`  
- Error, when the list command is used as cluster is locked.    

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/error_lock.png)  

- Command to unlock the cluster:  
`docker swarm unlock`  

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/unlock.png)  

- Command to view the key once the cluster is accessible/unlocked:  
`docker swarm unlock-key`  

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/unlock_key.png)  

- Command to rotate the key:  
`docker swarm unlock-key --rotate`  

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/key_rotate.png)

- Once the key is rotated, the updated new key which is rotated should be used a new key to unlock the cluster. Old key will not work.  

<a name="binding"></a>
### Binding Volumes  
- Syntax to create a volume:  
`docker volume create --name <volume-name>`  
- Creating a service along with volume mount:  
`docker service create --name <service-name> --mount type=<type-of-mount>,source=<volume-name>,target=<target-path> <image-name>`  

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/volume_create.png)  

- The task is running on node swarm-3.  

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/mount_service_ps.png)  

- List the volumes on swarm-3 using the command:  
`docker volume ls`  

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/volume_ls.png)  

- Login to the container:  
`docker container exec -it 76fcb2060b22 bash`

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/container_login.png)  

- Create a file **sample.txt** in the container.  

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/file_create.png)  

- Check the sample.txt in the volumes directory, path: `/var/lib/docker/volumes/volume-name/_data`  

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/volume_file.png)  

- Even after deleting the container or the service, the data will be attached to the volumes and can be retrieved from the above path.  

<a name="placement"></a>
### Control Service Placement  
- Can control the placement of the services by specifying the constraints, preferences, and other placement-related options.  
- Syntax for creating a service:  
    `docker service create --name <service-name> --constraint node.labels.<key>==<value> --replicas <count> <image-name>`
- Example:  
    - Adding the region label to a node 'swarm-2' to 'San Francisco' using: 
        `docker node update --label-add region="sfo2" xoqmj05eoc3u4djk28b8wrw0k`  

    ![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/label_update.png)

    - Checking the region of the node using: `docker node inspect swarm-2`  

    ![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/inspect_node_placement.png)  

    - Creating a service in the nodes where the region is San Francisco using:  
        `docker service create --name mynginx --constraint node.labels.region=="sfo2" --replicas 2 nginx`  

    ![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/service_create_placement.png)  

    - Checking where the created tasks are running using: `docker service ps mynginx`  

    ![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/service_replicas.png)  

<a name="overlay"></a>
### Overlay Network  
- Syntax to create an overlay network:  
    `docker create network --driver overlay <network-name>`  

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/create_overlay.png)  

- Verify the network using: `docker network ls`  

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/network_ls.png)  

- Creating a service in the overlay network using: 
    `docker service create --name mynginx --network myoverlay --replicas 2 nginx`  

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/service_overlay.png)  

- Syntax to create a secure overlay network (encrypted):  
    `docker network create --opt encrypted --driver overlay mysecureoverlay`  

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/secure_overlay.png)  

<a name="template"></a>
### Creating Services using Templates  
- Syntax to create a service using template:  
    `docker service create --name <service-name> --<flag>="{{.placeholder}}-{{.another-key}} <image-name>`  
- Command to create service using the hostname:  
    `docker service create --name mynginx --hostname="{{.Node.Hostname}}-{{.Service.Name}}" nginx`    

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/service_template.png)  

- Verify the hostname of the container/task:  

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/verify_template.png)  

<a name="mesh"></a>
### Routing Mesh  
- Create a service with 2 replicas on a port 8080:  
    `docker service create --name mynginx --publish published=8080,target=80 --replicas 2 nginx`  
    - Here, the mynginx service tasks are running on two nodes i.e, swarm-1 and swarm-2.  

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/service_create_mesh.png)  

- Try to access the service task for all the nodes, from the task which is running on swarm-1 we can access the service on swarm-3 node even though any task isn't running on it.  

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/verify_mesh.png)  

<a name="secrets"></a>
### Secrets  
- Syntax to create a secret: `docker secret create <secret-name> <secret-file-name>`  
- Command to create a secret from a file 'secret.txt':  
    `docker secret create mysecret secret.txt`  

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/secret_create.png)  

- When a secret is inspected nothing is displayed about the secret as it was encrypted:  
    `docker secret inspect mysecret`  

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/inspect_secret.png)  

- Command to create a service using the secret:  
    `docker service create --name mynginx --secret mysecret --replicas 2 nginx`  

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/secret_service.png)  

- As the containers are running on swarm-2 and swarm-3 nodes, trying to access the secrets from the container from the node swarm-3:  

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/access_secret.png)

<a name="leave"></a>
### Leaving From Cluster   
- Syntax for a node to leave the swarm cluster(run the command in the node that is set to leave the cluster):  
    `docker swarm leave`

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/swarm_leave.png)  

<a name="mtls"></a>
### MTLS  
- All the certificates are stored in the path:  `/var/lib/docker/swarm/certificates`  

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/mtls_certificates.png)

- Create a swarm cluster with one manager and one worker node.  

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/mtls_swarm_nodes.png)  

- Remove the worker node from the cluster.  
    - Worker node: `docker swarm leave`  

    ![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/mtls_swarm_leave.png)  

    - Manager: `docker node rm <node-name/id>`  

    ![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/mtls_swarm_rm.png)  

    - Nodes:  

    ![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/mtls_rm_nodes.png)

- Now, generate a new certificate or rotate the certificate on the manager node using:  `docker swarm ca --rotate`  

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/mtls_rotate.png)  

- When we try to join again the swarm using the old worker token, there will be a error... bcz the new certificate has been generated so the tokens will also change accordingly.  

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/mtls_swarm_join_again.png)  

- Join with a new generated worker token.  

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/mtls_swarm_join_new.png)  

- Calculate the checksum of the certificate on the worker node using: `md5sum <file-name>`  

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/mtls_md_before.png)  

- The above values will be changed again when a new certificate is generated as above:  

![screenshot](https://github.com/saimanasak/docker/blob/main/orchestrartion/swarm/screenshots/mtls_md_after.png)  

> [!NOTE]  
> All the above commands that manage the cluster should be done only in the **manager** node.  
