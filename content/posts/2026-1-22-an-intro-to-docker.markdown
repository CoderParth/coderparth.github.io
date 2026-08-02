---
layout: post
title:  "An Intro to Docker"
date: 2026-01-22
---



## What is Docker?

**Docker** is best understood with a problem that you likely have faced: the same code runs in your machine, but it does not run on your friend's/colleague's machine.

Docker solves this machine by packaging and running your application in a loosely isolated environment called a `container`. Containers contain everything that is required to run your application. 

## Docker objects

### Images: 
An image is a read-only template with instructions to build and run a Docker container. You might either create your own images, or use those that are available in Docker’s public registry. 

To create your own image, a Dockerfile is used, which contains simple syntax that defines the steps needed to create that image. Each instruction creates a `layer` in the image. When a Dockerfile is changed, only the layer that has changed is rebuilt. This is what makes the rebuilding process significantly faster, compared to other virtualization technologies. 

### Containers:
A container is a runnable instance of an image that you can create, stop, move, or delete using your CLI or Docker API. When you remove a container, all changes to its state that are not stored in a persistent disk are removed. 

## Example of `docker run` command

`docker run -i -t ubuntu /bin/bash`

The above command runs the ubuntu container, attaches your terminal directly to the container, and runs /bin/bash. 

**The following things happen under the hood:**

- If you don’t have “ubuntu” image locally, it is pulled from the configured registry, as if you had manually run `docker pull ubuntu`.
- It creates a new container, as if you had manually run the `docker create` command. 
- A read-write filesystem is allocated to the container to create or modify files and directories in its local filesystem. 
- A network interface is created to connect the container to the default network. 
- After the container starts, `/bin/bash` is executed. 


## `docker compose` Command

Docker Compose allows you to define all your containers and configurations in a single YAML file. This is a way to replace long `docker run` commands with a clean and small `docker compose` command without having to worry about managing networks, and all the flags needed to connect those containers. 

### Example of `compose.yaml` file:
`
services:
  web:
    build: .
    ports:
      - "8000:5000"
  redis:
    image: "redis:alpine"
`

This file defines **2 services**: `web` and `redis`. 

The `web` service will use the image built from the `Dockerfile` present in the current directory, and then map port `5000` of the container to the port `8000` of the host. This means that when you visit **localhost:8000**, Docker will send that traffic to the container’s port 5000. 

With this, you can easily start up your application by running `docker compose up`, and without worrying about manually configuring networks in the `docker run` commands.

## To tear this down

To remove everything, simply use `docker compose down`. By default volumes are not automatically removed. To remove the volumes, add the `--volumes` flag. 

`docker compose down --volumes`



## Building production-ready images

To create container images, a `Dockerfile` is used. It provides instructions to the image builder about the steps needed to build that image. These steps include: commands to run, files to copy, etc.

Below is an example of a Dockerfile with steps, for a simple API written in Go, listening on port 8080. 


### 1.
On the first line of your Dockerfile, you can add an optional directive line that instructs the Image builder to interpret your file according to the version you have specified.  
`#syntax=docker/dockerfile:1`  

You can then tell the image builder about the base image that you want to use for your application using the `FROM` keyword, in this case, we are using Go.  
`FROM golang:1.25.5`  

So, combining that it looks like this:  
```dockerfile
#syntax=docker/dockerfile:1

FROM golang:1.25.5
```

### 2.
Next step is to make it easy to run the rest of our commands, we can create a directory inside the image that we are building. From here on, we can just type the relative paths based on this directory, and not the full file paths.  
```dockerfile
WORKDIR /app
```

From here on, all the commands that we write will treat this directory as our current base directory. 

### 3.
The next thing you would usually do after you have downloaded your project on go, would be to install all the dependencies. In go. `go.sum` and `go.mod` handle all the required dependencies. So, the next step for us is to copy these files into our image, before we start installation. To this, we can use the `COPY` keyword, which takes two arguments, the first: the files that you want to copy, and second: the destination (the directory in which you want those files to be copied). So the next line will look like this:  

```dockerfile
COPY go.mod go.sum ./
```

Since we are already in our /app directory (because of the previous command), we can simply type `./` as our destination directory. 

### 4.
The next step is to install all the dependencies for your project. We can install all the required dependencies in Go using `go mod download`, and we can use the `RUN` command in Docker to tell the image builder to run it. So, our next line will be:  

```dockerfile
RUN go mod download
```

### 5.
Now, we can copy all our source code to our image. To do this, we can use the `COPY	` command again, just like the way we did on step 3.  

```dockerfile
COPY *.go ./
```

This copies all the files ending with `.go` extension to our current directory inside the image. 

### 6.
The next step is to compile our go program, and we can achieve this using the `RUN` command again.  
```
RUN CGO_ENABLED=0 GOOS=linux go build -o /docker-go-api
```

In case, you are not familiar with the compilation command:  
- `CGO_ENABLED=0`: This tells the compiler to disable CGO (a mechanism to call C code from Go). This is done to create a truly static binary that does not depend on external C libraries.  
- `GOOS=linux`: This tells the compiler that the targeted operating system will be linux. “Linux” is specified because our container will run Linux.  
- `go build`: This is the standard command to compile, and build a binary.  
- `-o`: This specifies the output filename and path.  
- `/docker-go-api`: Our binary will be called `docker-go-api`, and will be located in the root of the filesystem of our image.  

### 7.
The next step is to add documentation about the port this app will run on. This way, the person running this container will know which port is intended to be published. We can do this using the `EXPOSE` command.  
```
EXPOSE 8080
```

Now the person running this container will know that this app runs on port 8080.  
**NOTE:** `EXPOSE` command does not actually publish the port, it is added only for documentation. 

### 8.
The next and final command is to tell our image builder what to run when it starts a container. We can do this using the `CMD` command.  
```
CMD [“/docker-go-api”]
```

Now, when our container starts, it will run our binary “docker-go-api”.

Below you can find the complete file:

```dockerfile
# syntax=docker/dockerfile:1

FROM golang:1.25.5

WORKDIR /app

COPY go.mod go.sum ./

RUN go mod download 

COPY *.go ./

RUN CGO_ENABLED=0 GOOS=linux go build -o /docker-go-api

EXPOSE 8080

CMD ["/docker-go-api"]
```

Before we go onto the next part of building this image, let’s quickly create a small Go project, with one single API, in the same directory. 

### First, initialize a Go project: 
```
go mod init docker-go-api
```

### Create a `main.go` file: 
If you are on linux/mac, use the touch  command:  
```
touch main.go
```

Add this to your main.go file:

```go
package main

import (
	"github.com/gin-gonic/gin"
)

func main() {
	router := gin.Default()
	router.GET("/", func(c *gin.Context) {
		c.JSON(200, gin.H{
			"message": "Hello Docker!",
		})
	})
	router.Run() // listens on 0.0.0.0:8080 by default
}

```

The above code imports “gin” to create a small API, with a single endpoint “/”, that returns a JSON message:  
```
{"message": "Hello Docker!"}
```

Run ```go mod tidy``` to automatically import `gin` and to update `go.sum`.

Now, we are ready to build this.  



## Building this image

To build this image, we can use the `docker build` command. This will build images from the Dockerfile.

While building the image, you can optionally provide a string name for the name of the image. You can do this using the `--tag` flag. Below we will use the `--tag` file to name the image as `docker-go-api`. 

### Command:
```docker build --tag docker-go-api .```

The .(dot) at the end tells the image builder to look for dockerfiles in the current directory. 

When build is completed successfully, you will see a “Finished” log on your terminal. 

## Viewing local images 

To see the local images that are available/have been built on your machine, you can use `docker image ls` command. 

`docker image ls`

                                                                                                                                                                            
IMAGE                                    ID             DISK USAGE   CONTENT SIZE   EXTRA     
docker-go-api:latest                0b0a24003886       1.84GB          431MB    U   

## Running this container:

We can run the container using the `docker run` command. This command takes `image name` as the parameter. So, in our case, our command will be:

`docker run docker-go-api`

### Output:

```
[GIN-debug] [WARNING] Creating an Engine instance with the Logger and Recovery middleware already attached.

[GIN-debug] [WARNING] Running in "debug" mode. Switch to "release" mode in production.
 - using env:   export GIN_MODE=release
 - using code:  gin.SetMode(gin.ReleaseMode)

[GIN-debug] GET    /                         --> main.main.func1 (3 handlers)
[GIN-debug] [WARNING] You trusted all proxies, this is NOT safe. We recommend you to set a value.
Please check https://github.com/gin-gonic/gin/blob/master/docs/doc.md#dont-trust-all-proxies for details.
[GIN-debug] Listening and serving HTTP on 0.0.0.0:8080
```

You will see an output similar to the above, and it says that it is serving on localhost:8080. However, when you send a request, or even visit that site through your browser at that port, you will get a “Failed to connect” error.  `This is because by default the container’s network is isolated from the host network.` The container can access the outside world, but the host and other machines cannot access the container’s ports unless you publish them. 

To publish, you can use the `--publish` or just `-p` flag with the syntax `host_port:container_port` on the `docker run` command. So, here, in our case, if we do `3000:8080`, when we visit `3000` on our local host, our request will be forwarded to `8080` of the container’s port, which is where our api server is running. However, to keep it simple for our example, we will keep the host_port the same as the container_port, which is 8080. 

`docker run -p 8080:8080 docker-go-api`

So now, when you make a request to `localhost:8080`, you will get the response:
```
{"message":"Hello Docker!"}
```
