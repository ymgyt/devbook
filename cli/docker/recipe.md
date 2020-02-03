# Recipe


## Clean

```console
# remove all unused objects
# if you want to remove all unused volumens pass --volume flag
$ docker system prune [--volumes]

# remove all stopped containers
$ docker container prune

# remove dangling image
$ docker image prune
```