# Dockerfile
1. [ Intro ](#Intro)
2. [ Commands ](#Commands)
    - [ ADD ](#ADD)
    - [ ARG ](#2-arg)


<a name="Intro"></a>
### Intro
- A **Dockerfile** is a text document that contains all the commands a user can call on the command line to assemble an image. 
- To build an image we need to have an dockerfile.
- Docker images are the basis for the docker containers.
- Format:
```
#comment
INSTRUCTION arguments
```
- Instruction isn't case-sensitive.
- A dockerfile must begin with a **FROM** instruction (if you are using any parent image i.e., FROM specifies the base image from which we want to start creating the image from)

<a name="Commands"></a>
### Commands
- There are many commands that can be used in a dockerfile.

<a name="ADD"></a>
#### 1. ADD
- This instruction copies files, directories, or remote URLs from the source (build context) and adds them to the destination (image) of the filesystem.
- Has the ability to extract compressed archives and fetch files from the URLs automatically.
- Syntax:  
`ADD <src>...<dest>`
- Examples:
    ```
    ADD web.jar /website/ #Copies a local file into the image
    ADD src/ /website/ #Copies a local directory into the image
    ADD archive.tar.gz /website/ #Extracts content of archived into directory
    ADD https://playdocker.com/docker.txt /website/ #Gets file from the URL specified and copies it to the directory
    ```
- If source is a file or directory, then the content of source are copied to the destination.
- If source is a URL, then the file is downloaded and placed in the destination.
- If destination path ends with a trailing slash (/) then it's a directory.
- Multiple sources can be specified

<a name="ARG"></a>
#### 2. ARG
- Used to define a build-time variable that users can pass along with the docker build command.
- Should specify the build arguments that are defined in the dockerfile, if not a warning will be triggered.
- There can be multiple ARG instructions.
- Syntax:  
`ARG <variable_name>[=<default_value>]`
- In simpler form, the default values which are defined in the dockerfile using the ARG instruction can be overridden by passing along with the build command.
- If not specified during the build, the default values will be considered.
- Example:  
    ```
    FROM busybox
    USER ${name:-myname}
    ARG name
    USER $name

    docker image build --build-arg name=stranger
    ```

<a name="CMD"></a>
#### 3. CMD  
- It is a default command to run when a container is started from the built image.
- Can be overridden when running a container.
- Should be only one CMD instruction. If there are multiple, the last of the file is considered.
- Syntax:  
       `CMD ["executable", "param1", "param2"...] #exec form`  
       `CMD command param1 param2 ... #shell form`

<a name="COPY"></a>
#### 4. COPY  
- Used to copy files or directories from the build context i.e, from our local to the filesystem of the image that is being created.
- Syntax:  
    `COPY <src>...<dest>`
- Can also set permissions to files along with copying.
    `COPY --chown=username:groupname <src>...<dest>`
    `COPY --chown=UID:GID <src>...<dest>`
- Similar to ADD instruction.

<a name="ENTRYPOINT"></a>
#### 5. ENTRYPOINT  
- Sets the main executable command and any default arguments.
- Cannot be overridden.
- Syntax:  
    `ENTRYPOINT ["executable", "param1", "param2"...] #exec form`  
    `ENTRYPOINT command param1 param2 ... #shell form`

<a name="ENV"></a>
#### 6. ENV  
- Used to set environment variables in the image.
- key-value pair  
- Syntax:  
    `ENV <key>=<value>...`
- Multiple pairs can be set at once.
    ```
    ENV APP_NAME=myweb \
        APP_VERSION=1.0
    ```
- Can use the environment variables in the other instructions as well.
    ```
    ENV APP_NAME=myweb \
        APP_VERSION=1.0

    WORKDIR /app
    COPY . /app

    LABEL version=$APP_VERSION
    ```
- Can be overridden during the build process using the **--build-arg** flag.
    ```
    ENV APP_NAME=myweb \
        APP_VERSION=1.0

    WORKDIR /app
    COPY . /app

    LABEL version=$APP_VERSION

    > docker build --build-arg APP_VERSION=2.0 -t image_name
    ```

<a name="EXPOSE"></a>
#### 7. EXPOSE  
- Informs Docker that the container listens on a specific network ports at runtime.
- It just serves as a documentation feature to inform the users about the ports that to be exposed.
- Syntax:  
    `EXPOSE <port>/<protocol>...`  
- There two protocols: **TCP** and **UDP**. Default protocol is TCP.
- When we run a container, we use **-p or --publish** flags to map ports between the host and container.  
    `docker run -p 8080:80 image_name`  
- Values of EXPOSE can be overridden.
- It is not necessary to define EXPOSE instruction in the Dockerfile.
- Can define multiple ports at once.  
    `docker run -p 8080:80 -p 3030:30 image_name`

<a name="FROM"></a>
#### 8. FROM  
- Used to specify the base image from which we are building the image. 
- Syntax:  
    `FROM <base_image>[:tag] [AS name]`  
    `FROM [--platform=<platform>] <image>[:<tag>] [AS <name>]`  
- Both tag and alias name are optional.
- Another syntax:  
    `FROM [--platform=<platform>] <base_image> [AS <name>]`
- Only ARG instruction can be used before FROM. 
  Example:  
    ```
    ARG  CODE_VERSION=latest
    FROM base:${CODE_VERSION}
    CMD  /code/run-app
    ```

<a name="HEALTHCHECK"></a>
#### 9. HEALTHCHECK  
- To define a command that Docker will run periodically to check the health of a running container.
- Can specify customized health check commands.  
- Syntax:  
    `HEALTHCHECK [options] CMD command` (checks by running the command)  
    `HEALTHCHECK NONE` (disables healthcheck that is inherited from the base image)  
- Options that can be defined before CMD are:  
    `--interval=DURATION` (default: 30s)  
    `--timeout=DURATION` (default: 30s)  
    `--start-period=DURATION` (default: 0s)  
    `--start-interval=DURATION` (default: 5s)  
    `--retries=N` (default: 3)  
- Only one HEALTHCHECK instruction in the file.  
- Command's exit status:  
    - **0**: success/healthy  
    - **1**: failed/unhealthy  
    - **2**: reserved/encountered an error      

<a name="LABEL"></a>
#### 10. LABEL  
- Adds metadata to an image.
- key-value pairs.
- Can be used to provide info about an image.
- Syntax:  
    `LABEL <key1>=<value1> <key2>=<value2>...`   
- Can have multiple labels and can also be defined in a single line.  
- Labels can be inherited from the specified base image (overrides with the most recently applied value)

<a name="MAINTAINER"></a>
#### 11. MAINTAINER  
- deprecated
- supports older versions of Docker.
- bit similar to LABEL instruction.

<a name="ONBUILD"></a>
#### 12. ONBUILD  
- Used to add instructions that will be executed during a later build stage i.e, when the our already image is used as base image for another image.
- Syntax:  
    `ONBUILD INSTRUCTION`  
- Can have multiple ONBUILD instructions.

<a name="RUN"></a>
#### 13. RUN  
- Used to execute commands during the build process.
- Syntax:  
    `RUN <command>`  

<a name="SHELL"></a>
#### 14. SHELL
- USed to set the default shell that will be used during for RUN instructions.
- Syntax:  
    `SHELL ["executable", "parameters"]`  
- Can be overridden.
- Usage:  
    ```
    #Bash
    SHELL ["/bin/bash", "-c"]

    #PowerShell
    SHELL ["pwsh", "-command"]

    #Custom script
    SHELL ["/path/my_script.sh", "param1", "param2"]
    ```  
- This impacts the subsequent **RUN**, **CMD**, and **ENTRYPOINT** instructions i.e., all these 3 will be using the shell that is specified by SHELL instruction.

<a name="STOPSIGNAL"></a>
#### 15. STOPSIGNAL  
- Used to set the system call signal that will be sent to the container to stop it gracefully.
- Syntax:  
    `STOPSIGNAL signal`  
- i.e., when we run a 'docker conatiner stop' command, then the Docker will send the signal to the primary process that is running on the container, allowing it to perform any cleanup or any other shutdown procedures before stopping.
- Can be overridden by using **--stop-signal** flag along with the docker run and create commands.

<a name="USER"></a>
#### 16. USER
- Set the user that will be used to run the commands.
- Syntax:  
    `USER <user>[:<group>]`  
    `USER <UID>[:<GID>]`  
- i.e., this allows us to run the commands specified in the image with a specified user.
- By default, it runs as root user.

<a name="VOLUME"></a>
#### 17. VOLUME 
- Used to create a mount point and declare that the specified directories within the container should be externally mounted as volumes.
- Syntax:  
    `VOLUME ["/data"]`  
- Volume starts as an empty directory.
- Even if the data is specified in the dockerfile, during the build time the specified data will be discarded.
- Example:  
    `docker run -v /localhost/path:/web image_name`  
    Here, this commands mount's the host machine's '/localhost/path' directory to the '/web' directory inside the container as a volume.

<a name="WORKDIR"></a>
#### 18. WORKDIR  
- Used to set the working directory for any RUN, CMD, ENTRYPOINT, COPY, and ADD instructions that follow it in the dockerfile.
- Defines the default directory where the specified commands to be executed.
- Syntax:  
    `WORKDIR /path/of/directory`  

<a name="CMD and ENTRYPOINT"></a>
#### CMD and ENTRYPOINT
- CMD: commands and arguments can be overridden,  
  Example:  
    ```
    CMD ["npm", "start"]

    #Here, 'npm start' command will be overridden by 'npm test' command
    > docker run image_name npm test
    ```  
      
  ENTRYPOINT: can't be overidden but we append additional arguments.  
  Example:  
    ```
    ENTRYPOINT ["python", "web.py"]
    CMD ["arg1", "arg2"]

    #Here, arguments are appended to the main executable command
    > docker run image_name python web.py custom_arg1 custom_arg2
    ```
- CMD can be used to provide default arguments for the command that is specified in ENTRYPOINT.  
    ```
    ENTRYPOINT ["python", "web.py"]
    CMD ["arg1", "arg2"]
    ```