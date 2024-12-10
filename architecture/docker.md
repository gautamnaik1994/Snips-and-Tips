# Docker

## Docker Commands

Command to inspect the content of an image created

```bash
docker run -it --name temp_container imageName /bin/sh
```

where "temp_container" is the name of temporary container

```bash
docker images
```

to list images

```
docker build -t server .
```

to build images where "server' is the name of image

```bash
docker run --rm -p 8000:8000 name_of_image
```

Add `--rm` to remove the container after it stops
