# Dockerize a Node.js Web Service

The code used in this example and the article itself is also available in my [Samples Git repo](https://github.com/CharlesSieg/samples/tree/master/basic-dockerize).

To build the Docker image:

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

Don't miss the period at the end of that statement. Docker will chug for awhile as it downloads the various layers it needs for the image.

Once the image has been created, its existence can be verified in the local repository:

```
docker images

REPOSITORY          TAG                 IMAGE ID            CREATED              SIZE
basic-dockerize     latest              43ea15c8a66f        About a minute ago   680MB
node                carbon              292a11903b0d        4 days ago           676MB
```

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

To obtain a list of containers:

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

To remove the container:

```
docker container rm e2299030ac2a

e2299030ac2a
```

To remove the image:

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

