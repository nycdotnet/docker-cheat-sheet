# Docker Cheat Sheet


## Example dockerfile

Assuming you have a file called `myapp.js` in the current directory that you'd like to run a Docker container with Node 10.10.0:

```dockerfile
FROM node:10.10.0-slim
COPY ./myapp.js /app/
CMD ["node","app/myapp.js"]
```


## Docker compose

Launch a docker-compose service in daemon mode (non-interactive)
`docker-compose up -d servicename`

Stop a docker-compose service.
`docker-compse down`

(not docker-compose, but relevant to next item)
`docker volume ls`

Stop services and delete named volumes and anonymous volumes attached to containers
`docker-compose down -v`
