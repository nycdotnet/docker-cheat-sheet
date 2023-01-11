# Docker Cheat Sheet


## Example dockerfile

Assuming you have a file called `myapp.js` in the current directory that you'd like to run a Docker container with Node 10.10.0:

```dockerfile
FROM node:10.10.0-slim
COPY ./myapp.js /app/
CMD ["node","app/myapp.js"]
```

You can build the `Dockerfile` in the current directory with `docker build .`.  If you need to pull a newer version of the images (specified as part of `FROM`), you can use `--pull` as in `docker build . --pull`, otherwise the images will be cached.  This is usually required when using a floating tag such as `latest`.  To specify a `Dockerfile` other than the default use `-f` such as `docker build -f some-other.dockerfile .`

## Running a pulled image

You can pull down an image from Dockerhub or another registry using a command like: `docker pull redis` or `docker pull jaegertracing/all-in-one`.  Once you have pulled down an image, you can list the images available with `docker images`.  These images can be run using the values of the `REPOSITORY`, `TAG`, and `IMAGE ID` fields in the output.  Consider this example output of `docker images`:

```
REPOSITORY                                            TAG              IMAGE ID       CREATED         SIZE
redis                                                 latest           3e42dd4e79c7   6 days ago      117MB
redis                                                 <none>           53aa81e8adfa   2 months ago    117MB
postgres                                              11               60a93af0bba5   6 weeks ago     284MB
jaegertracing/all-in-one                              latest           b4407f47570d   4 weeks ago     60.4MB
jaegertracing/all-in-one                              <none>           5dc67a620878   2 months ago    60.3MB
jaegertracing/all-in-one                              <none>           115e9ce6a360   6 months ago    57.5MB
```

You could run the latest redis locally by repository name with the command `docker run redis` or `docker run redis:latest`.  The `latest` tag is implied if omitted when using a name.  If you wanted to run the older version of redis (from "2 months ago" in the above output), you can use the image id instead: `docker run 53aa81e8adfa`.

## Tagging an image

If you want to refer to a particular image id by a custom tag, you can use `docker tag` command.  For example, if I wanted to refer to the version of redis from "2 months ago" in the above listing of `docker images` as the `old` tag, I could do that by running this command using its image id: `docker tag 53aa81e8adfa redis:old`.  Then I could run it as `docker run redis:old`, and when I run `docker images`, it would be shown like this in the output:

```
REPOSITORY                                            TAG              IMAGE ID       CREATED         SIZE
redis                                                 old              53aa81e8adfa   2 months ago    117MB
```

See the [docker tag](https://docs.docker.com/engine/reference/commandline/tag/) documentation for more details.

### Environment variables

You can pass environment variables to a container that you launch with docker run via `-e` such as `docker run -e SOME_VAR=FOO`.  If you don't put the `=` then the value of your local environment variable will be passed into the container.  There is also support for env files.  See https://docs.docker.com/engine/reference/commandline/run for more details.

### Ports

You can expose ports in a container to the host OS.  For example `docker run -p 127.0.0.1:5432:5432/tcp` will expose port 5432 in the container to port 5432 on 127.0.0.1 of the host OS.  The "local" port is first and the port as it exists in the container is second.

## Running a container interactively

Once you've built a Linux-based `Dockerfile`, you can run bash inside it using the image hash (will be shown at the end of each successful step in the build such as `Successfully built 8ecf61de9be9`).  The command is typically `docker run -it <image> bash`, though bash may be `bin/bash` or even something else, depending on the setup of the image.  If the `Dockerfile` has an `ENTRYPOINT` step, you may have to specify bash as an entrypoint instead: `docker run -it --entrypoint /bin/bash <image>`  .  For a Windows container, use `cmd` as the entrypoint instead of `/bin/bash`.

If you run `docker ps` to get the container ID, you can run `docker exec -it <containerid> /bin/bash` .

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

Delete dangling images (generally safe, and may reclaim lots of storage)
`docker image prune`

Delete images not used by existing containers (don't do unless you can redownload stuff that's not running!!)
`docker image prune -a`

## Extending an existing image

I wanted to run Postgres 10.11 using the `postgres:10.11` image, but I also wanted to run some configuration on server startup in order to have a user there for doing local testing.  I created a Dockerfile like this:

```
FROM postgres:10.11

COPY ./*.sql /docker-entrypoint-initdb.d/
```

I had a `.sql` file in the same directory as the Dockerfile which was then copied into the image in a special place that causes it to be executed by the Postgres container on startup.  This SQL file contained the commands to create a test user and database.

Because I didn't make a new `CMD`, my new container kept the `CMD` of the [existing image](https://github.com/docker-library/postgres/blob/0d0485cb02e526f5a240b7740b46c35404aaf13f/10/Dockerfile#L176), so it ran `postgres` on boot.

I ran `docker build .` to build the image.  I then copied the hash of the image from the success message (was `Successfully built 09ec509c3bba`) and was then able to launch the container in daemon mode (allows it to run in the background and returns your console to you).  I also bound localhost port 5432 to port 5432 in the container (to allow connecting to the containerized postgres from my local computer only).  Final command to run:

```
docker run -p 127.0.0.1:5432:5432 -d 09ec509c3bba
```

You can see the container running via `docker ps`, which will also show the port mapping.  You can also see the port mappings alone via `docker port <container id>`

## Windows containers

Example Windows 10 Dockerfile (for Windows 10, 2020 H2) that installs Chocolatey and kubectl - (note the two different examples of RUN syntax):

```
FROM mcr.microsoft.com/windows:20H2

RUN ["powershell", \
    "Set-ExecutionPolicy Bypass -Scope Process -Force;", \
    "[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072;", \
    "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))"]

RUN choco install kubernetes-cli --no-progress --yes

CMD ["cmd", "/c", "dir"]
```

Build with `docker build .` just like with Linux.  Once built, `docker run <image>` to run as a script, or `docker run -it <image> cmd.exe` to run interactively.

## Miscellanea

To limit the memory that a particular container will use, `docker build` and `docker run` both take a `-m` switch, such as `-m 2GB`.

To set up a volume mapped between your computer and the container, you can use `-v localpath:containerpath`.  For a Windows container, this might be `-v c:/path/on/my/computer:c:/path/in/container`.
