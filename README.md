# Docker Cheat Sheet


## Example dockerfile

Assuming you have a file called `myapp.js` in the current directory that you'd like to run a Docker container with Node 10.10.0:

```dockerfile
FROM node:10.10.0-slim
COPY ./myapp.js /app/
CMD ["node","app/myapp.js"]
```
