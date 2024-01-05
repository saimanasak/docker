### Dockerfile
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

### Commands
- There are many commands that can be used in a dockerfile.

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

#### 3. CMD  
- It is a default command to run when a container is started from the built image.
- Can be overridden when running a container.
- Should be only one CMD instruction. If there are multiple, the last of the file is considered.
- Syntax:  
       `CMD ["executable", "param1", "param2"...] #exec form`  
       `CMD command param1 param2 ... #shell form`

#### 4. COPY  
- Used to copy files or directories from the build context i.e, from our local to the filesystem of the image that is being created.
- Syntax:  
    `COPY <src>...<dest>`
- Can also set permissions to files along with copying.
    `COPY --chown=username:groupname <src>...<dest>`
    `COPY --chown=UID:GID <src>...<dest>`
- Similar to ADD instruction.

#### 5. ENTRYPOINT  
- Sets the main executable command and any default arguments.
- Cannot be overridden.
- Syntax:  
    `ENTRYPOINT ["executable", "param1", "param2"...] #exec form`  
    `ENTRYPOINT command param1 param2 ... #shell form`

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