# Docker Cheat Sheet


## Example dockerfile

Assuming you have a file called `myapp.js` in the current directory that you'd like to run a Docker container with Node 10.10.0:

```dockerfile
FROM node:10.10.0-slim
COPY ./myapp.js /app/
CMD ["node","app/myapp.js"]
```

You can build the `Dockerfile` in the current directory with `docker build .`.  If you need to pull a newer version of the images (specified as part of `FROM`), you can use `--pull` as in `docker build . --pull`, otherwise the images will be cached.  This is usually required when using a floating tag such as `latest`.

## Running a container interactively

Once you've built a Linux-based `Dockerfile`, you can run bash inside it using the image hash (will be shown at the end of each successful step in the build such as `Successfully built 8ecf61de9be9`).  The command is typically `docker run -it <image> bash`, though bash may be `bin/bash` or even something else, depending on the setup of the image.  If the `Dockerfile` has an `ENTRYPOINT` step, you may have to specify bash as an entrypoint instead: `docker run -it --entrypoint /bin/bash <image>`

If you run `docker ps` to get teh container ID, you can run `docker exec -it <containerid> /bin/bash` .

If your container is running in Kubernetes, the command is `<kubectlalias> exec [--namespace <namespaceName>] -it <pod_name> -- /bin/bash`

## Docker compose

Launch a docker-compose service in daemon mode (non-interactive)
`docker-compose up -d servicename`

Stop a docker-compose service.
`docker-compse down`

(not docker-compose, but relevant to next item)
`docker volume ls`

Stop services and delete named volumes and anonymous volumes attached to containers
`docker-compose down -v`

Delete dangling images (generally safe and may reclaim lots of storage)
`docker image prune`

Delete images not used by existing containers (don't do unless you can redownload stuff that's not running!!)
`docker image prune -a`
