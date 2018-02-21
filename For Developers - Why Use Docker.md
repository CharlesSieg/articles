# For Developers: Why Use Docker?

It seems that no matter what kind of programmer you are, [Docker](https://www.docker.com) is a hot technology. Docker has only been around since March 2013 but it has already become an indispensable tool in every developer's toolbox. There are a number of excellent reasons why this is the case. But first, a quick primer on what Docker actually is.

Docker is a tool which can package an application (like a Node.js web service) and all of its dependencies into a virtual container that can run, essentially, anywhere. This is more formally known as "operating-system-level virtualization". The process of packaging an application in this way is called "containerization".

Whereas virtualization technology allows many virtual machines to run on a single physical server, Docker allows many containers to run on a single virtual machine. Docker containers do not include an operating system but rather share the virtual machine's underlying operating system with other containers. However, while containers share the operating system, they are otherwise completely isolated from each other.

The lack of an operating system makes Docker containers much more lightweight. In fact, they are so lightweight that it is not uncommon to have a more than a dozen Docker containers deployed on a single virtual machine.

While all of this sounds great for DevOps and other infrastructure professionals, there are powerful benefits for developers as well. To illustrate these benefits, consider the following scenario.

> You are a full-stack mobile application developer. You have recently joined a development team where you will be building a native iOS application in Swift. The iOS application connects to a Node.js web service for authentication, authorization, real-time data sync, logging, and other capabilities. During development, each developer runs the web service locally and the iPhone Simulator connects to that local web service for testing. On your first day, the team lead sends you some instructions on how to prepare your MacBook so that you can run the web service locally. As it turns out, the web service is pretty complex and, in addition to the Node.js code, makes use of a Redis cache, a MySQL database, and a RabbitMQ message queue, all of which need to be installed locally. Further, the web service uses an older version of Node.js. This is a problem for you because you have other client projects on the same MacBook which use the latest Node.js and which also run another Redis cache.

This scenario presents a number of challenges:

1. Every developer's machine has to run the web service locally. Normally that means manually installing a bunch of extra software: [Node.js](https://nodejs.org/en/), [Redis](https://redis.io), and [RabbitMQ](https://www.rabbitmq.com). [MAMP](https://www.mamp.info/en/) would probably be used to install and manage [MySQL](https://www.mysql.com). The developer needs to make sure all of the ports are set up correctly and that all of the services are running.

2. Since two versions of Node.js are now needed, [nvm](https://github.com/creationix/nvm) must be used to switch between Node.js versions.

3. ...

Containerization with Docker solves all of these issues.

## Portability

Docker containers are portable. This means that once the container has been created, it can run on any laptop, server, or virtual machine and execute the same way everywhere. The container is responsible for dependencies, not whatever happens to be inst



## Efficiency

Using virtual machines is another way to solve these challenges. However, virtual machines are large and resource-intensive. Spinning up a virtual machine takes forever and competes strongly with the host computer for memory and CPU. In contrast, Docker containers use a fraction of the resources and spin up almost instantly. Further, Docker is intelligent enough to share common parts of multiple containers.

