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
