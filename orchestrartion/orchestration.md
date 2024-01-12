#### Orchestration  

##### What is Orchestration?  
- It generally refers to the coordination and management of multiple processes, or systems, or components to work together accordingly to achieve a goal.  
- In Docker, it refers to as managing and coordinating multiple containers to deploy, scale, and maintain the large complex applications in a distributed environment.  
- i.e., it manages the life cycles of the containers in large and dynamic environments.  

##### Use-case  
###### Problem:  
- We have 3 virtual machines and the requirement is to run minimum of two web-server applications all the time.  
- But, both the applications are running in a single machine.  
- Issues:  
    - resource issue if the traffic is high.  
    - availability; not highly available i.e., if one vm goes down both the 2 running containers will be down.  

##### Solutions  
- There are many available solutions for container orchestration, few of them are:  
    - Docker Swarm  
    - Kubernetes  
    - Amazon ECS (Elastic Container Service)
    - Google Kubernetes Engine  
    - Apache Mesos  
