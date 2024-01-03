#### Image
- It is a file that contains all the necessary dependencies and configurations which are required to run an application.

#### Container
- It is a running instance of an image.
- A Universally Unique Identifier (UUID) when a container is created.
- By default, docker assigns a randomnly generated name containing of 2 words and combined by an underscore.
- We can also give a desired name to our container by  

    ```text
    --name=some_desired_container_name
    ```

#### Port Binding
- By default, containers can communicate with the outside world but the outside world cannot communicate with the containers.
- So for this , we have a process called port binding.
- This maps a host port to the container port.
    - Host port: It is a port on the host machine to which docker binds and this allow external communication with the application/services running inside a container.
    - Container port: It is a port exposed by a container in which service/application is running and this listens for the incoming connections.

#### Daemon
- It manages the docker containers and is responsible for creating, running, and managing the docker containers on a host system.
- When we run a "docker run" command then docker CLI sends a command to the daemon and which then executes it.
- We can find the config file of docker daemon in the below path:  

    ```
    /etc/docker/daemon.json
    ```

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

#### Default Container Commands
- When we start a container, a default command is executed that runs as PID1.
- This default command can be defined when we define a container image.
- Can override this command.

#### Docker 'exec'
- Docker container exec command is used to run a new command in a running container.
- This can be used only when the PID1 (i.e., the primary process) is running.
- If we stop the primary process then the container is stopped/terminated.
- This will not be restarted if the container is restarted.

#### 'it' flags
- Every process that is created in the linux environment has three file descriptors: stdin, stdout, and stderr.
- 'it flag'
    - **--i** is an interactive flag that keeps stdin open even if it's not attached.
    - **--t** is a tty flag that allocates a pesudo-TTY (which means it gives us a terminal to work on)

#### Restart Policies
- By default, containers will not start when they exit or when daemon is restarted.
- Docker provides few restart policies to decide whether the containers should start automatically when they exit or when the daemon restarts.
- Use **--restart** flag with the run command.

#### Removing
- To remove a running container, first we need to stop the container and then remove.
- We cannot directly remove a running container.
- If we use **--** flag then it automatically removes the container then it exits.