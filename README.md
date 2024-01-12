# Main Commands

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