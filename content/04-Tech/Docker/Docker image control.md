Container build from image
## images

```bash
# see all images
docker images
docker rmi <image_id_or_repo:tag>

# remove
docker rmi <image_id_or_repo:tag>
```
## container
### Search container
```bash
docker ps
docker ps -a
```

![[Docker_image_control_p1.png]]

### Remove container
```bash
# docker stop <id_or_name>
# docker rm <id_or_name>

docker rm e5adf266f9bd
```