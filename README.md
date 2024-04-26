# Getting started

This repository is a sample application for users following the getting started guide at https://docs.docker.com/get-started/.

The application is based on the application from the getting started tutorial at https://github.com/docker/getting-started

Dockerfile reference: https://docs.docker.com/reference/dockerfile/

Build the image
` docker build -t getting-started .`
`-t` tags image
`.` tells Docker to look for Dockerfile at that directory

Run the app:
` docker run -dp 127.0.0.1:3000:3000 getting-started`
`-d` means detach - runs container in background (docker starts container and returns you to terminal prompt)
`-p` means publish - creates a port mapping between host and container 
(takes a string value of HOST:CONTAINER where host is address or host and container is the port on the container)

Hosted on 3000

You can stop and remove a container in a single command by adding the force flag to the docker rm command. 
For example: `docker rm -f <the-container-id>`

Create and tag an image
Log in first: `docker login -u kate49` 
Create docker tag: `getting-started kate49/getting-started`
Push to docker image repo: `docker push kate49/getting-started`

Start the app with a internal one file db: 
`docker run -dp 127.0.0.1:3000:3000 --mount type=volume,src=todo-db,target=//etc/todos getting-started`

A volume mount is a great choice when you need somewhere persistent to store your application data.

A bind mount is another type of mount, which lets you share a directory from the host's filesystem into the container. 
When working on an application, you can use a bind mount to mount source code into the container.
Named volume: `type=volume,src=my-volume,target=/usr/local/data`
Bind mount: `type=bind,src=/path/to/data,target=/usr/local/data`

Run the following command to start bash in an ubuntu container with a bind mount.
`docker run -it --mount type=bind,src="$(pwd)",target=/src ubuntu bash`

Run a development container with a bind mount
`docker run -dp 127.0.0.1:3000:3000 -w /app --mount type=bind,src="$(pwd)",target=/app node:18-alpine sh -c "yarn install && yarn run dev"`
The following is a breakdown of the command:

`-dp 127.0.0.1:3000:3000` - same as before. Run in detached (background) mode and create a port mapping
`-w /app` - sets the "working directory" or the current directory that the command will run from
`--mount type=bind,src="$(pwd)",target=/app` - bind mount the current directory from the host into the /app directory in the container
`node:18-alpine` - the image to use. Note that this is the base image for your app from the Dockerfile
`sh -c "yarn install && yarn run dev"` - the command. 
You're starting a shell using sh (alpine doesn't have bash) and running yarn install to install packages 
and then running yarn run dev to start the development server. 
If you look in the package.json, you'll see that the dev script starts nodemon.
At this point, you can persist your database and see changes in your app as you develop without rebuilding the image.

Connect to MySQL database:
`docker run -d --network todo-app --network-alias mysql -v todo-mysql-data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=secret -e MYSQL_DATABASE=todos mysql:8.0`
To confirm you have the database up and running, connect to the database and verify that it connects.
`docker exec -it <mysql-container-id> mysql -u root -p`
mysql> SHOW DATABASES;
mysql> exit

