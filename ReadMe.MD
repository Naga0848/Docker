#### This repo we will understand about Docker

#### Dockerfiles

#### In this Dockerfiles Repo, we are just understanding the different instructions which we use in our Dockerfile and that is the reaon we mention CMD and give a sleep command to run the container.


#### Commands to install Docker on an EC2

    sudo dnf -y install dnf-plugins-core

    sudo dnf config-manager --add-repo https://download.docker.com/linux/rhel/docker-ce.repo

    sudo dnf install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

    sudo systemctl restart docker

    sudo systemctl enable --now docker

    sudo usermod -aG docker ec2-user


#### add your normal user to docker group  -- because we cannot run the docker commands as a normal ec2-user


    exit, after adding normal user and re-login and start using the docker commands without giving sudo

    Again after logging in, we have to start and enable the docker

    sudo systemctl start docker
    sudo systemctl enable docker


#### Docker Basic Commands

    docker images 

    docker pull <image-name>:<tag/version> --> get the image

    docker create nginx

    docker ps -a --> all containers including all status

    docker start <container-ID>

    docker rm <container-ID>

    docker run <image>:<tag> -> pull image + create container + start container

    docker run -d nginx --> detach the screen

    docker ps a -q ====> to display all the containers

    docker rm -f 'docker ps a -q'  ====> to delete all the containers at a time

    0-65,535   === total no of ports for a container or a server

    docker run -d -p 80:80 nginx

    docker exec -it nginx bash   ===> to login to a container

    docker inspect container-name/container-ID


#### General Notes

    How can you create custom images to your applications?

    Dockerfile --> a set of instructions to create customised images

    FROM
    =========
    FROM almalinux:9

    docker build -t from:v1 . --> current directory has Dockerfile

    RUN
    =========
    RUN commands

    RUN instructions configure the image like installing packages, doing some configurations, etc..
    RUN executes at the time of image creation

    systemctl start nginx --> etc/systemd/system/nginx.service

    docker pull nginx -> first it checks locally, if it does not exist it checks in hub

    CMD
    =========
    CMD executes at the time of container creation i.e at the time of docker run. there should be only one CMD instruction inside Dockerfile

    COPY
    =========
    copies the code from local to container

    ADD
    =========
    COPY and ADD both copies the code from local to container. but it has two more advantages

    1. it can directly fetch the file from internet
    2. it can directly untar the file into container

===================================================================

#### Docker-roboshop

    All these images are optimized and these images have less size

    As we are not filling any data into the Redis we can directly run the redis container on our Docker host

    Command >>>> docker run -d --name redis --network roboshop redis:7
    indicates that redis is running on roboshop network with the name redis

    As we are creating the Rabbitmq directly via docker compose and we are mentioning the env variables as well in the docker compose file


 #### My Docker Credentials
      username - nagashankar1992332

      login to the docker and run docker compose command


####  /var/lib/docker ----> Docker Home directory and all the images that we built are stored here and we need to increase the size of this location when   we are creating docker using terrform

#### DOCKER BEST PRACTISES

    # Dockerfile and Image Optimization:

    Use Multi-stage Builds: Separate build-time dependencies from runtime dependencies to create smaller, more secure final images.
    Choose Small Base Images: Start with lightweight base images (e.g., Alpine) to reduce image size and attack surface.
    Minimize Layers: Combine related commands in a single RUN instruction using && to reduce the number of image layers and improve build cache utilization.
    Leverage Build Cache: Order Dockerfile instructions to maximize caching, placing frequently changing instructions later in the file.
    Use .dockerignore: Exclude unnecessary files from the build context to speed up builds and reduce image size.
    Prefer COPY over ADD: COPY is generally safer and more predictable as it only copies local files, while ADD can also fetch remote URLs and extract archives.
    Pin Base Image Versions: Specify exact image tags (e.g., node:18-alpine) to ensure reproducible builds and prevent unexpected changes.
    Don't Install Unnecessary Packages: Only include what's essential for your application to run.

    # Container Security:

    Run as Non-Root User: Create a dedicated user inside the container and run the application as that user, reducing potential privilege escalation.
    Limit Capabilities: Restrict container capabilities to only those strictly necessary for the application.
    Enable Rootless Mode: Where possible, run Docker daemon and containers in rootless mode for enhanced security.
    Use Trusted Images: Source images from official repositories or trusted vendors and scan them for vulnerabilities.
    Avoid Exposing Sensitive Information: Do not store secrets or credentials directly in Dockerfiles or images. Utilize Docker Secrets or environment variables with caution.
    Secure the Docker Daemon Socket: Avoid exposing /var/run/docker.sock to containers or the network.

    # Deployment and Management:

    Create Ephemeral Containers: Design containers to be stateless and easily replaceable.
    Decouple Applications: Run only one primary process per container to maintain clear separation of concerns.
    Implement Health Checks: Include HEALTHCHECK instructions in your Dockerfile to allow Docker to determine if a container is running correctly.
    Use Custom Networks: Create custom Docker networks for better isolation and control over inter-container communication.
    Integrate with CI/CD: Automate image building, testing, and deployment within your CI/CD pipeline.
    Monitor and Log: Implement robust logging and monitoring solutions to track container performance and identify issues.


## Optimized Roboshop Dockerfiles

### Catalogue

    ikkada line no-7 lo unna mongodb:27017 is the host. manam separate ga route53 ni em use cheyatledu. name of the container is the name of the host i.e., 
    mongdb
    we already know that mongodb is available on 27017 port no
    line 5 installs dependencies
    line 3 and 4 makes sure that we are copying all the code into the /opt/server folder 
    And finally, line no-8, manam execute cheyalsina code server.js


    # Download the https://roboshop-artifacts.s3.amazonaws.com/catalogue-v3.zip  into our laptop and extract it. And copy the make package.json and server.j
    so that they are with our dockerfile

    # Below are the steps in understanding the CMD instruction step-by-step

    CMD ["node", "server.js"]
    CMD is the default command that gets executed when a container is started from the image you're building.

    🧠 What CMD Does
    It tells Docker: “When someone runs a container from this image, execute node server.js.”

    This starts your Node.js application by running the server.js file.
=============

    the word node refers to the Node.js runtime—a JavaScript engine that lets you run JavaScript code outside of a browser, typically on servers.

    🧠 What node Does Here
    It launches the Node.js interpreter.

    It runs the file server.js, which is presumably your backend application or server logic.

    So when the container starts, it’s essentially doing:

    node server.js
    inside the container, which kicks off your app
============

    Your Dockerfile starts with:

    FROM node:20
    This means your image is based on the official Node.js version 20 image, which already includes the node command. So you don’t need to install Node.js 
    manually—it’s baked into the image.

    # Commands to execute the docker image

    docker build -t catalogue:v1 .

    docker run -d --name catalogue catalogue:v1

    docker ps

    docker exec -it catalogue bash

    curl http://catalogue:8080/health   >>> to see whther catalogue is properly istalled or not

    exit

    docker have 2 types of network, bridge and host. host means directly host network. bridge means docker creates seperate network interface and assign the
    address to containers..

    docker default bridge network can't communicate between containers, docker always suggest to create custom brige network

    # Commands to create a new network and disconnect and connect to the new network

    docker network create roboshop  >>>  command to create a new network

    docker network disconnect bridge catalogue

    docker network disconnect bridge mongodb

    docker network ls >>> shows our networks


    docker network connect roboshop catalogue

    docker network connect roboshop catalogue

    docker exec -it catalogue bash
    curl localhost:8080/health
    this shows app:OK and mongo:true   >>>> which confirms that catalogue is running fine

### Cart

    As this is the nodejs application(microservice) the explanation is same like catalogue, so we are just mentioning the commands 
    the complete dockefile 

    Commands to execute the docker image

    docker build -t cart:v1 .

    docker run -d --name cart --network roboshop cart:v1

    docker ps 

    docker exec -it cart:v1 bash

    curl localhost:8080/health

    this shows app:OK and mongo:true   >>>> which confirms that cart is running fine

    exit
