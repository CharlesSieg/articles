# Dockerize a Node.js Web Service

There are lots of reasons for wanting to run applications in containers and I've covered some of these in another article. This article is about actually getting into some code and dockerizing a simple Node.js web service.

The code used in this example is available in my [Samples Git repo](https://github.com/CharlesSieg/samples/tree/master/basic-dockerize). This walkthrough assumes you have already installed Node.js. If not, follow the instructions on the [Node.js website](https://nodejs.org/en/). This walkthrough was tested on macOS but the same steps should work, more or less, in any OS.

## Step 1. Create the web service.

First, let's create a very simple "Hello World!" web service using Node.js. You can either clone my [Samples repo](https://github.com/CharlesSieg/samples) and skip down to step 2 or follow these steps to quickly create a new Node.js web service.

```
mkdir dockerize
cd dockerize
npm init -f
```

This creates a folder called **dockerize** and uses *npm* to install a default instance of **package.json**.

Open up the **package.json** file and change

`"main": "index.js"`

to

`"main": "server.js"`

You don't *have* to do this but the example code uses a JavaScript file named **server.js**.

Next, install the *Express* framework:

`npm install express -save`

This will download the *Express* package from *npm* and install it in the **node_modules** folder. It will also update the **package.json** file to reflect that *Express* is now a dependency.

Next, create an empty file called **server.js** and add the following:

```javascript
'use strict';

const express = require('express');
const PORT = 8080;
const HOST = '0.0.0.0';
const app = express();

app.get('/', (req, res) => {
    res.send('Hello World!\n');
});

app.listen(PORT, HOST);
console.log(`Running on http://${HOST}:${PORT}`);
```

This JavaScript code basically uses *Express* to create a web service that will listen on port 8080 and respond to any incoming *GET* request with the text "Hello World!"

Now, start the Node.js web service:

```
npm start

Running on http://0.0.0.0:8080
```

and confirm that it is running correctly by opening a new terminal window and typing:

`curl http://127.0.0.1:8080`

You should see

`Hello World!`

Congratulations, you now have a working Node.js web service! Go ahead and hit *Control+C* in the terminal window where the web service is running to terminate the web service.

## Step 2. Create the Dockerfile.

The Dockerfile is a text file which contains all of the instructions, in order, to build a given Docker image. It's basically a script: Docker executes the instructions in the script and the end result is a new Docker image.

In the same folder as the rest of the web service code, create a new file called **Dockerfile**. Copy the following text into your **Dockerfile** and save it:

```
FROM node:carbon

WORKDIR /usr/src/app

COPY package*.json ./

RUN npm install

COPY . .

EXPOSE 8080

CMD ["npm", "start"]
```

Now, let's examine this Dockerfile, line by line:

`FROM node:carbon`

The **FROM** instruction tells Docker to initialize a new build stage and set the [Base Image](https://docs.docker.com/engine/reference/glossary/#base-image) to an initial image. This instruction must be the first instruction in any Dockerfile.

The image here is provided by the Node.js Docker Team and contains the Carbon release of Node.js which is, essentially, [Node v8.9.0](https://nodejs.org/en/blog/release/v8.9.0/) and is in Long Term Support (LTS) until April 2019. This is a solid image to start off with but `node:alpine` is also very good (and optimized for containers). We'll use `node:carbon` for this exercise.

`WORKDIR /usr/src/app`

The **WORKDIR** instruction sets the default working directory for running binaries within the container. Without this instruction, the default is the root directory.

`COPY package*.json ./`

This **COPY** instruction copies the package.json (and package_lock.json if present) into the root directory of the container's file system. This is to prepare for the next line:

`RUN npm install`

Without going into too much detail, this **RUN** instruction actually executes *npm install* and causes all of the web service's dependencies to be installed. This basically creates the **node_modules** folder which contains the *Express* package.

`COPY . .`

This instruction copies all of the files in the current folder - the **server.js** file mainly - into the current folder in the image. After this instruction is finished, the source code for the web service has been completely installed in the Docker image.

`EXPOSE 8080`

The **EXPOSE** instruction lets Docker know that the container listens on the network port 8080 at runtime.

`CMD ["npm", "start"]`

The **CMD** instruction takes the form of `["executable", "param1", "param2"]` and is intended to provide defaults for an executing container based off of this image. The Dockerfile can only contain one **CMD** instruction.

That's it for the Dockerfile. We're ready to use the Dockerfile to build an actual image.

## Step 3. Build the Docker image.

To build the Docker image (don't miss the period at the end of the statement):

```
docker build -t basic-dockerize .

Sending build context to Docker daemon  1.943MB
Step 1/7 : FROM node:carbon
carbon: Pulling from library/node
4176fe04cefe: Pull complete 
851356ecf618: Pull complete 
6115379c7b49: Pull complete 
aaf7d781d601: Pull complete 
936f8420f2e4: Pull complete 
b098f1cb38c6: Pull complete 
158bd11ba716: Pull complete 
7c31a2433b70: Pull complete 
Digest: sha256:e54e8d37bd8c85b3f607a4978d0d50c21cba0e6a3e132651ea0f19b5dfc76203

Status: Downloaded newer image for node:carbon
 ---> 292a11903b0d
Step 2/7 : WORKDIR /usr/src/app
Removing intermediate container 4c8c5dd86d89
 ---> 6486c47da7e8
Step 3/7 : COPY package*.json ./
 ---> 421040d50539
Step 4/7 : RUN npm install
 ---> Running in c9fd55b82ac4
npm WARN package@1.0.0 No repository field.
added 49 packages in 2.716s
Removing intermediate container c9fd55b82ac4
 ---> 9737793779cb
Step 5/7 : COPY . .
 ---> fdb74606209c
Step 6/7 : EXPOSE 8080
 ---> Running in 99154c17918f
Removing intermediate container 99154c17918f
 ---> ad6da0c5f373
Step 7/7 : CMD [ "npm", "start" ]
 ---> Running in a7d02ac05ba4
Removing intermediate container a7d02ac05ba4
 ---> 43ea15c8a66f
Successfully built 43ea15c8a66f
Successfully tagged basic-dockerize:latest
```

Docker will chug along for awhile as it executes all of the instructions in the Dockerfile. The longest instruction is the initial **FROM** which causes the `node:carbon` base image to be downloaded. The resulting image is tagged *latest*.

Once the image has been created, its existence can be verified in the local repository:

```
docker images

REPOSITORY          TAG                 IMAGE ID            CREATED              SIZE
basic-dockerize     latest              43ea15c8a66f        About a minute ago   680MB
node                carbon              292a11903b0d        4 days ago           676MB
```

## Step 4. Run the container.

To run a new container using the image:

```
docker run -p 8181:8080 -d basic-dockerize

e2299030ac2ab0e9fc976dc774870e3668e2bf4b86cbef4ca560ac85d965b298
```

The *-p* option maps port 8181 of the host to port 8080 of the container. The *-d* option causes the container to be run in the background and to output a long identifier that is the unique identifier of the container.

Verify that the web service running in the container is responding by using *curl*:

```
curl http://127.0.0.1:8181

Hello World!
```

Notice that the web service is running on port 8080 within the container and that requests on host port 8181 are being successfully routed to 8080.

Congratulations! That's all there is to getting a Node.js web service up and running inside of a Docker container.

## Step 5. Stop and remove the container and image.

It's always good to know a bit about cleaning up. Containers can be stopped and deleted. Images can be deleted if there are no containers left which are built from the image.

A container can be stopped if you know its container ID. This is easily obtained by listing the containers:

```
docker ps

CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                    NAMES

e2299030ac2a        basic-dockerize     "npm start"         12 minutes ago      Up 12 minutes       0.0.0.0:8181->8080/tcp   keen_raman
```

Stop the container with:

```
docker stop e2299030ac2a

e2299030ac2a
```

Confirm that the container has stopped by running `docker ps` again and verifying the absence of the container's process in the list.

Now that there are no more running instances of the container, it can be removed:

```
docker container rm e2299030ac2a

e2299030ac2a
```

Finally, now that there are no more containers referencing the image, the image itself can be removed:

```
docker rmi basic-dockerize

Untagged: basic-dockerize:latest
Deleted: sha256:43ea15c8a66fd40fa394a201a18e1debd1e488058d8545dac98316b3044bf7d7
Deleted: sha256:ad6da0c5f373544da6b4747c54e8f35cbd6288d39b50c0b679b1a2432db5dc62
Deleted: sha256:fdb74606209c68924b210f0d0cddd184968e110f3dbe034410acaf90eae8636d
Deleted: sha256:7dc6ec49c42551958bb2e1aa6dea943667e060bd383c574293f65dadb6546011
Deleted: sha256:9737793779cb874335cbb5609581bfc064a5dc270857869175c6a393251d69a4
Deleted: sha256:ef0b3d05675c6464b411e014b58f245847c42242cb1f5522f1e29463ef4d1317
Deleted: sha256:421040d5053906105d8baef48251499a25c00416c4d1d7a6cc6988ee4513d1c9
Deleted: sha256:42c86b69f072cbe1b450db7b92608281853174de66fcbf1a3b395abc639c78b2
Deleted: sha256:6486c47da7e8c98b5fb13ac06c21b8d9f34d45ae7e243bf49cb95298726ef6c2
Deleted: sha256:24262066a0c3bb10836010d85f9091c93d7cb53bf8b170ac5f02f76fb71ebbe4
```

