# Docker volume restore

Accidentally deleted a docker volume?
Have a backup?

you will need:

- the backup path for /var/lib/docker/volumes
- the name of the volume from the compose file

The process is

1. stop the container
2. create the volume
3. copy the data from the backup to the new volume
4. start the container

``` bash
docker stop <container> 
# or from the directory with the compose file in it docker compose down
docker volume create <volume name>

# sudo because the directory is owned by root
sudo rsync -av --progress <backup location>/var/lib/docker/volumes/<volume name>/_data/ \ /var/lib/docker/volumes/<volume name>/_data/

docker compose up -d
```