# Part 1 - Main Commands

#### List all images: 

```bash
docker image ls
docker images
```

#### Download image from registry:

```bash
docker image pull <image_name>
docker pull <image_name>
```


<em><u>NOTE</u>:</em> The image name consists of 2 parts: The name and its tag -->  **<image_name> = <<name:tag>>**

#### Remove an image:

```bash
docker image rm <image_name>
docker rmi <image_name>
```

<em><u>NOTE</u>:</em> If you run a container before using the image you want to delete, it will NOT let you delete it. You need to force it with the <strong>-f</strong> flag such as:

```bash
docker image rm -f <image_name>
docker rmi -f <image_name>
```

#### Run a container:

```bash
docker container run <image_name>
docker run <image_name>
```
<em><u>NOTE</u>:</em> Docker will run commands in the container and once it finishes it will stop then quit. For code development, we want to access the terminal. Thus we need to add the **-it** flag.

**-i**: Interactive & **-t**: TTY
It means, give me a terminal that I can type in.

```bash
docker container run -it <image_name>
docker run -it <image_name>
```

#### List all running containers: 

```bash
docker container ls
docker ps
```

<em><u>NOTE</u>:</em> This gives use useful infos such as the **CONTAINER ID** and the **CONTAINER NAME**

#### Stop a container: 
 Method 1: We can use **ctrl+D** from the running terminal

 Method 2:

 ```bash
docker container stop <container_name>
```

#### List all containers (even if they stopped): 

```bash
docker container ls -a
docker ps -a
```

#### Remove containers (even if they stopped): 
 
 ```bash
docker container rm <container_name>
```

<em><u>NOTE</u>:</em> To remove **ALL** containers at once:

 ```bash
docker container prune
```

<em><u>NOTE</u>:</em> Rather than deleting your container manually afterwards, you can use the **-rm** flag, which will delete it automatically when its done.

```bash
docker container run -rm <image_name>
docker run -rm <image_name>
```

#### Name a container:

```bash
docker container run --name <container_name> <image_name>
docker run --name <container_name> <image_name>
```

#### Important Note:

We can run multiple copies of the same image at once, by using the **docker run** command. Thus, each one will get its own container with container name.

But sometimes we just want to execute some commands inside the same container. Fo that we use the **docker exec** command:

```bash
docker exec -it <container_name> <commands>
```
Here are 2 examples of commands that we can execute:

1. Open a new terminal inside the container

    ```bash
    docker exec -it <container_name> /bin/bash
    ```
2. Run **ls** inside the container

    ```bash
    docker exec -it <container_name> ls
    ```

# Part 2 - Writing a Dockerfile

This allows use to make permanent changes to an image.

That is the place where we install packages. For that we will use **apt**.

<em><u>NOTE</u>:</em> In a Docker container everyhting runs as **sudo**, so no need to bother with that. On the contrary, sometimes we do note to run things as root, but that is for future work.

A Dockerfile consists of a file containing **Dockerfile instructions** or **commands**, and they are written in uppercase.

 * **FROM** must be the first instruction in a Dockerfile. It specifies the base image for the build, that suits our application. In our robotics application, we will choose **ros:galactic** as the base image.

    ```bash
    FROM base_image:tag
    ```
 * **RUN** executes commands during the image build process.

    ```bash
    RUN command_to_run
    ```
 * **CMD** provides default command and/or parameters for an executing container.
 
    ```bash
    CMD ["command", "arg1", "arg2"]
    ```

 * **COPY** copies files or directories from the build context to the container.
 
     ```bash
    COPY source destination
    ```
 * **ADD** copies files, directories, or remote URLs from the build context or a URL to the filesystem of the container.

    ```bash
    ADD source destination
    ```

 * **ENV** sets environment variables inside the container.
    ```bash
    ENV key=value
    ```

 * **ARG** defines a build-time variable that users can pass at build-time to the builder with the docker build command using the --build-arg flag.

    ```bash
    ARG variable_name=default_value
    ```

 * **USER** sets the username or UID to use when running the image. It is often used to switch from the default root user to a less privileged user for security reasons.

    ```bash
    USER user_name_or_uid
    ```

 * **WORKDIR** sets the working directory inside the container.

    ```bash
    WORKDIR /path/to/working/directory
    ```
    
 * **VOLUME** creates a mount point and marks it as holding externally mounted volumes from native host or other containers. 

    ```bash
    VOLUME /path/to/volume
    ```
    
 * **ENTRYPOINT** specifies the command that will be run when the container starts.

    ```bash
    ENTRYPOINT ["executable", "param1", "param2"]
    ```






