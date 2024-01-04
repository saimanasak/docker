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