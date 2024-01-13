# Table of Contents
 - [Main Commands](#main-commands)
 - [Writing a Dockerfile](#writing-a-dockerfile)
 - [Share files from Host to Container](#share-files-from-host-to-container)
 - [Solving common-issues related to Robotics](#solving-common-issues-related-to-robotics)
   - [Change the ROS base image](#change-the-ros-base-image)
   - [Adding a regular user](#adding-a-regular-user)
   - [Running with different users](#running-with-different-users)
  
 
# Main Commands

#### Build an image:

```bash
docker image build -t <image_name> <path_to_Dockerfile>
docker build -t <image_name> <path_to_Dockerfile>
```
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

# Writing a Dockerfile

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

# Share files from Host to Container  

There are 2 methods for this:
* Volumes
* Bind mounts

Volumes are like a shared virtual drive that you cannot easily access from outside Docker, but you can make it available to any container that need it.

**Bind mounts are more helpful for our application, we can simply  mount the directory with our source code into the container, allowing our files to be accessed**

Lets do that right now:

```bash
docker run -it -v <absolue_path_host>:<absolute_path_in_container>
```

Now we can edit from the container and it will map to our host to the appropriate path.

**IMPORTANT:** The files created from the container that are present in our host will **be owned by root**. It is creating files as **root**, this can be a problem because we cannot access them easily from the Host.


# Solving common-issues related to Robotics

## Change the ROS base image

I order to access GUI applications and have access to more packages we will change the ROS base image to: **osrf/ros:humble-desktop-full**. This will contain RViz for example and thus will be more heavy.

## Adding a regular user

By default Docker runs as **root** for practicl/security reasons.  Creating files from the container that are bind mounted creates files owned by the root, making them non-accessible from the host machine.

By adding a regular user, we will solve this problem. Thus instead of running as root we will run as a regular user, which will be the same as the host.

We will solve this by going through the following steps:

**STEP 1:** Add following to the Dockerfile: 

```bash
ARG USERNAME=ros
ARG USER_UID=1000
ARG USER_GID=$USER_UID

# Create a non-root user

# creates a new group named $USERNAME with numerical group ID $USER_GID
RUN groupadd --gid $USER_GID $USERNAME \ 
    # Adds a new user with username $USERNAME, assignsa UID, sets the login shell to '/bin/bash', assigns the user toa primary group w/ a specofied GID
    && useradd -s /bin/bash --uid $USER_UID --gid $USER_GID -m $USERNAME \
    && mkdir /home/$USERNAME/.config && chown $USER_UID:$USER_GID /home/$USERNAME/.config
```

**STEP 2:** Build image:

```bash
docker build -t <image_name> .
```

**STEP 3:** Run container with **--user** argument:

```bash
docker run -it --user <username> -v <absolue_path_host>:<absolute_path_in_container> <image_name>
```

<em><u>NOTE</u>:</em> They are many valid combinations but the most common ones are: 

```bash
--user <uname>
--user <uname>:<gname>
--user <uid>
--user <uid>:<gid>
```
<em><u>NOTE</u>:</em> The order of docker run arguments does not matter as long as they come before the image name.


## Running with different users

In the Dockerfile you can swap betwwen **root** and other user by using the Dockefile instruction **USER**. Everything written after that will be run as specified user, thus you can switch between users.

<em><u>NOTE</u>:</em> Whatever user is set last using the **USER** instruction will be the **default user* used when the container is run. Although you can overwrite that with the **docker run --user** command.

One problem, is that if you end your Dockerfile using another user and then someone else goes to extend from your image, to create their own one, they won't necessarily realize that any command that they are executing are not being run as root. They can fix this by starting their Dockerfile with the user root: 

```bash
USER root
```

But it is good practice to end the Dockerfile with teh user root and then just use the **docker run --user** command to select which user you want when you start the container.

## Setting up sudo to create files as root (if other user are added)

This is not really necessary since you can use **docker exec** to create a new terminal in the container with **root** access. But it make things easier.

**STEP 1:** Add to Dockerfile: 

```bash
# Set up sudo
RUN apt-get update \
   && apt-get install -y sudo \
   && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME\
   && chmod 0440 /etc/sudoers.d/$USERNAME \
   && rm -rf /var/lib/apt/lists/*
```

**STEP 2:** Create root file inside container using **sudo** command

```bash
sudo <file_name>
```

This will create a **root** file.

## Tricks when using apt-get


1. Why do we use **apt-get** instead of **apt** ?

   **apt** not supposed to be used in automted tasks and scripts like Dockerfiles.
   **apt-get** meant to be more reliable and compatible and used in scripts.

2. Why run **apt-get update** every time?

   It updates teh package list, and if you do not update it, then yo could be working with an ol version from a previous layer and end up with some incompatibilities.

3. Why do we use **-y** ?

   This avoids user to select **yes/no** to install programs and and forces to answer **yes** to the questions.

4. Why do we delete the package lists (**rm -rf /var/lib/apt/lists/**) at the end of each one ?

   This saves on size in the final image. It also prevents someone from forgetting to run **apt-get update** before future install coammands

5. Why set environment variable **DEBIAN_FRONTEND=noninteractive** ?

   This ensures that you do not get other user prompts during the install process.
