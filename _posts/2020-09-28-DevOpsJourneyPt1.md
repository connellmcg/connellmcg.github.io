---
layout: post
title: A DevOps Learning Journey (Part 1)
subtitle: Docker & Getting Started
tags: [devops, docker, learning]
comments: true
---


## A DevOps Learning Journey (Part 1: Docker)


## What is the aim of this:

I wanted to learn a little about DevOps & the technologies and terms around it such as CI/CD, Docker, Kubernetes and so on. It's hard to ignore these terms when you are upskilling in the world of cyber security, so I thought I'd spend time figuring these all out and implementing them. 

This quick fly-by / walkthrough mainly focuses on Docker and getting a simple React 'Hello World' app into a container and onto Github.

I followed a brilliant Udemy course, [Docker and Kubernetes: The Complete Guide](https://www.udemy.com/course/docker-and-kubernetes-the-complete-guide/), to learn more about this and I took notes. This article is built off the notes I took during the course. 

The second & third parts (coming soon) will delve into the implementation of the workflow and the actions taken when new code is pushed up onto the Github repository.

Feedback on this is really welcome - please let me know if there are any inaccuracies or areas I have missed/could improve. :)

## Pre-Requisites

 - [Node](https://nodejs.org/en/download/)
 - [Docker](https://www.docker.com/) 
 - Your favourite CLI (WSL2/bash etc)
 - Eventually AWS, Travis CI and Azure accounts.

## Why Use Docker 

Historically there has been a problem for developers setting up and sharing working environments and one of the reasons why Docker is so popular is that it gets around a lot of these initial setup woes by 'containerising' the dependencies and modules required for applications. 

This allows you to get up and running quickly with preconfigured environments without having to worry about setting up or installing the  (sometimes very specific) dependencies.

As the container is self sufficient, it can be shared easily with other individuals or teams and you can be confident they will be able to run it also. 

Docker containers are known to be 'immutable'. This means that a running container never really changes, and if it shuts-down or restarts, the running image is reverted to the original 'blank slate'.

In any case, let's just get on with the building of the app and container. The references at the end should give a greater insight into the workings of Docker.


## Building a React App (Really Quickly)
We just want to go from zero to hero here -  create a react app called docker-react and drop into the folder.

    npx create-react-app docker-react && cd docker-react
    
You can test launching it by run the `npm run start` command.
    

## Building a Docker Container
This is where it starts to get interesting & fun :)

Docker containers are built using either Dockerfiles or through `docker-compose` and a yml file.

For this we need to build the app using two Dockerfiles. One will be Dockerfile.dev and the second will be the production Dockerfile.

We could just build the app using a single Dockerfile and upload it, and that would be enough. But because we want to learn and play with CI/CD, we will need create a Dockerfile.dev - which will be required for our CI/CD tests. The workflow will kick off the production build once the output of the Dockerfile.dev command returns an exit code of 0 (success).

    FROM node:alpine
    WORKDIR '/app'
    COPY package.json .
    RUN npm install
    COPY . .
    CMD ["npm", "run", "start"]
    
Containers are built with base images. There are [many](https://hub.docker.com/search?q=&type=image&category=base) base images that can be chosen, but we have gone with Alpine Linux as the compiled container comes to 5MB in size. This makes it a popular base image for projects. There are other base images that might suit your project, for example [nginx](https://hub.docker.com/_/nginx), which among many things, is a suitable webserver for our project. So we'll use that too.


Docker defaults to work with files called 'Dockerfile', so we need to use the `-f` switch to force it to use our Dockerfile.dev file.

`docker build -f Dockerfile.dev .`

When you run this command, you will see that Docker is creating an image for each step of the build process. Docker is clever enough to know what has changed so it avoids unnecessary rebuilds by using it's own image cache stored locally. This is why we `COPY package.json .` first and the `RUN npm install` bit right after; so we don't need to rebuild the biggest and slowest bit every time we make a change to our code.
Any code changes will be pulled in via the `COPY . .` command, which should speed up subsequent container builds.
Once you run the build command, you can remove the node_modules folder from your source directory. The directory has already been imported into your container.

Once complete, you should see the output of your docker build command

![docker build output](https://github.com/connellmcg/connellmcg.github.io/blob/master/img/docker2.png?raw=true)

Run your docker image using 

    docker run -it -p8080:3000 09001a489a76
  
  The `-it` switch launches the container in terminal mode, as in you drop into a terminal session in the running container and you see the output of the command. I used it here so that the container wouldn't run and then shut down. (In production, you want to remove the `-it` switch to avoid ['the input device is not a TTY' issue.](https://thomasthornton.cloud/2020/08/10/the-input-device-is-not-a-tty-fix-when-running-docker-in-azure-devops/) )

The `-p8080:3000` switch maps the local machine port 8080 to port 3000 inside the container. 
Some people might tag an image using `docker run -it -p8080:3000 09001a489a76 -t connellmcg/docker-react:latest` - just in case you were wondering if you'd have to work with GUIDs all the time.

If you see this, all is good and we can move on.

![Docker](https://github.com/connellmcg/connellmcg.github.io/blob/master/img/docker1.png?raw=true)

You can edit `src/App.js` at this point and run `docker build -f Dockerfile.dev .` here to implement your changes. But there is a better way... 

## Docker Volumes
When we update files in the docker container, it looks like we have to rebuild the container every time we make a change. Can we do this without having to rebuild the container every time? Yes, using docker volumes. 

Example command: `docker run -p 8080:3000 -v /app/node_modules -v $(pwd):/app <image_id>`

`-v $(pwd):/app` : map the pwd (present working directory) into the '/app' folder inside the container. When we use a colon, we are saying we want to map a folder inside the container to a folder outside the container. In this case, the files in the $(pwd) would contain the source files for the react app.

This gives us the ability to update the source files and see the changes in the web browser without us having to rebuild the container.

`-v /app/node_modules`: We need to tell docker to use/reference the node_modules folder that is inside the container as we removed the node_modules folder from the local system in the earlier build step. This puts a bookmark on the node_modules folder. Note how no colon is used. 


## Docker Compose

docker-compose makes executing docker run a lot easier, especially for development workflows. If you look at the previous command to start our container, there is a lot going on. `docker run -p 8080:3000 -v /app/node_modules -v $(pwd):/app <image_id>`

By using docker-compose, we can set this command out using a yml file.

    version: '3'
    services:
      web:
        stdin_open: true
        build:
          context: .
          dockerfile: Dockerfile.dev
        ports:
          - "8080:3000"
        volumes:
          - /app/node_modules
          - .:/app
          
	  
Here you can see we are still using the Dockerfile.dev from the current directory (context: .) and the port and volume mappings are clearly defined.

To kick off docker-compose, we use the command `docker-compose up` to initiate the container instances using the docker-compose.yml file, and to bring it down, you use `docker-compose down`.
 
## Running Tests using docker-compose

When we move on to using CI technologies like Travis CI, we will be executing tests as part of our workflow. We can implement tests in docker containers also, and use that knowledge to help us build out the tests when we move on to CI.

Our default command in the Dockerfile.dev is  `CMD ["npm", "run", "start"]`

We can override this command by telling the image to run the `npm run test` command by executing `docker run -it <image id> npm run test`
This issue can be problematic in automated workflows as it requires you to have the `<image id>`  handy. 

To get around this, we can use docker-compose to set out the test conditions.
We can add on a tests section to the previously created docker-compose.yml file:

    tests:
      build:
        context: .
        dockerfile: Dockerfile.dev
      volumes:
        - /app/node_modules
        - .:/app
      command: ["npm", "run", "test"]

## Why Nginx? (& Multi Step Builds)

When we run the `npm run start` we are launching a development server that serves up the web files for us to run in our own browser. This is great for our testing and development work, but its definitely not cool for production.  When we do the  `docker run build` command, it compiles without the development server. We need a way to serve these files (as we don't have the dev server in the package any more, d'oh!), and that's where [nginx](https://hub.docker.com/_/nginx) comes into play.

‌We are going to build a new *multi-stage* docker container for our production code and serve it using nginx. There are two actions happening here. The first is running the `npm run build` command which is compiling our react app, without the dev server, and outputting it into `/app/build`.

The second part is pulling down an nginx image and moving the newly built files into the default directory for serving web files in nginx. This configuration setting can be found here. Finally the `EXPOSE 80` command is telling the container to open up port 80 to serve the web requests.

    FROM node:alpine
    WORKDIR '/app'
    COPY package.json .
    RUN npm install
    COPY . .
    RUN npm run build
    
    FROM nginx
    EXPOSE 80
    COPY --from=0 /app/build /usr/share/nginx/html



## Pushing to Github
Without going into too much detail about github, there are a number of actions required in order to publish what we have done so far. Feel free to `git clone` my [repo](https://github.com/connellmcg/docker-react) if you want to have a play around with the files yourself.

 - [ ] Create a Github Repo
	 - [ ] Create one on your own github - mine is called docker-react
 - [ ] Create a local git repo
	 - [ ] `git init`
	 - [ ] `git add .`
	 - [ ] `git commit -m "initial commit"`
 - [ ] Connect the local repo to the remote repo
	 - [ ] `git remote add origin git@github.com:connellmcg/docker-react.git`
 - [ ] Push changes up to the main repo
	 - [ ] `git push origin main`


## Next Steps
Now that the code has been pushed online to Github, the next steps will be to set up the integration that watches for code updates, runs tests and if they pass, automatically deploy. 

This is a good time to end Part 1 as the code here in its current state is a good jumping off point to various CI/CD workflow technologies. 

Part 2 will use Travis CI and AWS for this.
Part 3 will use Azure DevOps.

## *References*
My [Github](https://github.com/connellmcg/docker-react) for this project

A huge proportion of my learning came from this Udemy course: [Docker and Kubernetes: The Complete Guide](https://www.udemy.com/course/docker-and-kubernetes-the-complete-guide/)

Scott Hanselman's intro to Docker (which kicked off my interest): [Docker 101 Explained: Computer Stuff They Didn't Teach You #8](https://www.youtube.com/watch?v=0oEsMwSxBsk)

[Docker 101 Fundamentals](https://itnext.io/docker-101-fundamentals-the-dockerfile-b33b59d0f14b) by Paige Niedringhaus


