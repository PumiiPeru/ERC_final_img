
## Using Docker

---
**NOTE**

The commands in this section should be executed as the `root` user, unless you have configured docker to be [managable as a non-root user](https://docs.docker.com/engine/install/linux-postinstall/).

---

Make sure the [Docker Engine](https://docs.docker.com/engine/install/#server) is installed and the `docker` service is running:
```
systemctl start docker
```
Build the docker image by executing:
```
docker build -t erc_sim .
```
Permit the root user to connect to X window display:
```
xhost +local:root
```
Start the docker container:
```
docker run --rm -it -v /tmp/.X11-unix:/tmp/.X11-unix -e DISPLAY --name erc_sim erc_sim
```
If you want the simulation to be able to communicate with ROS nodes running on the host or another docker container, add `--net=host` flag:
```
docker run --rm --net=host -it -v /tmp/.X11-unix:/tmp/.X11-unix -e DISPLAY --name erc_sim  erc_sim
```
Gazebo can work really slow without the GPU acceleration. \
If you are running the system on an integrated AMD/Intel Graphics card, try adding `--device=/dev/dri` flag:
```
docker run --rm --device=/dev/dri -it -v /tmp/.X11-unix:/tmp/.X11-unix -e DISPLAY --name erc_sim  erc_sim
```
To use an Nvidia card, you need to have proprietary drivers installed, as well as the [Nvidia Container Toolkit](https://github.com/NVIDIA/nvidia-docker). \
Add the `--gpus all` flag and set `NVIDIA_DRIVER_CAPABILITIES` variable to `all`:
```
docker run --rm -it -v /tmp/.X11-unix:/tmp/.X11-unix -e DISPLAY --gpus all -e NVIDIA_DRIVER_CAPABILITIES=all --name erc_sim erc_sim
```
To start any other ROS nodes inside the container, type:
```
docker exec -it erc_sim /ros_entrypoint.sh <COMMAND>
```
For example:
```
docker exec -it erc_sim /ros_entrypoint.sh roslaunch leo_viz rviz.launch
```
To update the docker image, you need to rebuild it with `--no-cache` option:
```
docker build --no-cache -t erc_sim .
```
