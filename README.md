# Docker-Dev

NVIDIA DOCKER CONTAINER ISSUE

When build a Dockerfile
The temp Container for building time does not have "/usr/local/nvidia/" and "/dev/nvidia/*"
So build library with CUDA inside Dockerfile maybe failed Because "No Such File or Directory"


HOWTO USE DOCKER CONTAINER

1.Build Docker Images:

nvidia-docker build --force-rm -t image-id:tag .  # for currnet path
nvidia-docker build --force-rm -f /path/to/Dockerfile -t image-id:tag .  # for remote path

2.List local images:

nvidia-docker images

3.How to Start a Dev Container:

nvidia-docker run -ti --rm --name=your_dev_name -p 8024:22 --privileged --device /dev/bus/usb:/dev/bus/usb image-id:tag Opentional_COMMAND

Optional:
with -v hostPath:containerPath that can mount Host file or directory to Container

4.Exxcure a COMMAND on a running Container

nvidia-docker exec -ti -user=username container-id COMMAND

5.List running and not running Container

nvidia-docker ps -a

6.Start and Stop the Container

nvidia-docker start container-id
nvidia-docker stop container-id

7.Copy file between Host and Container

nvidia-docker cp hostpath container-id:containerpath
nvidia-docker cp container-id:containerpath hostpath

8.Remove a not running Container

nvidia-docker rm container-id

Optional:
Remove All not running container
nvidia-docker rm $(nvidia-docker ps -aq)

9.Remove a non-used image

nvidia-docker rmi image-id

Optional:
Remove All untagged images:
nvidia-docker rmi $(docker images | grep "^<none>" | awk "{print $3}")



