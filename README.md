# React + Vite

This template provides a minimal setup to get React working in Vite with HMR and some ESLint rules.

Currently, two official plugins are available:

- [@vitejs/plugin-react](https://github.com/vitejs/vite-plugin-react/blob/main/packages/plugin-react/README.md) uses [Babel](https://babeljs.io/) for Fast Refresh
- [@vitejs/plugin-react-swc](https://github.com/vitejs/vite-plugin-react-swc) uses [SWC](https://swc.rs/) for Fast Refresh

# this project is for learning how to dockerise a reactjs application

# Use an official Node.js LTS image
FROM node:14 as builder

# Set the working directory in the builder stage
WORKDIR /app

# Copy only the package.json and package-lock.json files
COPY package*.json ./

# Install dependencies
CMD npm install

# Copy the rest of the application files
COPY . .

# Build your application (if necessary)
# RUN npm run build

# Production image
FROM node:14-slim

# Set the working directory in the production stage
WORKDIR /app

# Copy only the necessary artifacts from the builder stage
COPY --from=builder /app .

# Expose the desired port through an environment variable
ENV PORT=5173
EXPOSE $PORT

# Use an environment variable for the start command
CMD ["npm", "run" ,"dev"]


# now we are going to build the docker image with this command
command : docker build /path/to/where/Dockerfile/exists 
command : docker build .
# build the image with a tag/name
command : docker build -t react-docker .

# list all docker images 
command : docker image ls 

# delete a docker image 
command : docker image rm <ImageID>
command : docker image rm e3d72819aafe

# running and building a docker image over and over again is much faster due to how docker works [cashing !]

command : docker run -d [detached mode] --name [container name]  [image name]
command : docker run -d --name react-app-docker react-docker 

# difference between RUN && CMD 
	#RUN npm install:

	RUN is a Docker instruction that is used during the image build process.
	When you include RUN npm install in your Dockerfile, it is executed at build time, not when you run a container.
	This instruction is typically used to install project dependencies. It runs the npm install command, which reads the package.json file and installs the specified packages into the image.
	The purpose of this step is to ensure that the necessary dependencies are installed and available in the image, so your application can run successfully when the container is started.

	#CMD ["npm", "run", "dev"]:

	CMD is also a Docker instruction, but it is used to specify the default command to be executed when a container is started from the image.
	The CMD instruction defines the command and any arguments or parameters to be executed when the container is launched.
	In your case, CMD ["npm", "run", "dev"] specifies that when a container is started, it will automatically run the npm run dev command.
	This is typically used to start your Node.js application or any other primary process within the container.
	The CMD instruction is executed at runtime, when you use the docker run command to create and start a container based on the image.
# EXPOSE 5173
this command does not allow you to actually use the running app on your local machine. 
this command actually does nothing.
this command is for documentation purposes only because after building and running your app its now in an [ISOLATED CONTAINER] which is not accesible by your local machine.
# how to access the post 5173
command : docker run -d -p 5173:5173 --name react-app-docker react-docker


# allow your host to access any port on the docker container
command : docker run -d --network="host" --name react-app-docker react-docker 

Argument: --network="host" used the --network="host" option to allow a Docker container to share the network stack of the host machine,
enabling easier access to host services or exposing a service to the entire host network.

#log the application logs

command : docker logs react-app-docker


this command allows you to do that 

> react-docker@0.0.0 dev /app
> vite


  VITE v4.4.9  ready in 375 ms

  ➜  Local:   http://localhost:5173/
  ➜  Network: use --host to expose
# Inspect your docker container 
command : docker inspect react-app-docker

# run another command on your container 
docker exec -it [container-name] bash 
this allows us to interact with our container bash




#BINDING MOUNT TO SYNC SRC COD
command : docker run -v "$(pwd)/src:/app/src" -d --network="host" --name react-app-docker react-docker
#READ ONLY BIND MOUNTS
this is done so we prevent the docker container from making changes to our actual host system files 
we just want to docker container to RECIEVE changes and not make them

command : docker run -v "$(pwd)/src:/app/src:ro" -d --network="host" --name react-app-docker react-docker 
Argument : we added the [ro] [readOnly] 

#to start docker compose 
command : docker-compose up -d

#to stop docker compose
command : docker-compose down

#MULTI STAGE BUILD FOR PRODUCTION WITH NGINX
 
for production 
docker run -d -p 80:80 --name react-prod-app docker-image-prod
this is defualt nginx port 80
