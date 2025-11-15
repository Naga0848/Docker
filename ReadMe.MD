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