---
title: Application setup and dockerization
---

The following guide explains how to create Docker images for your application for deployment in a Kubernetes cluster. You'll use 'ExampleApp' as a reference. Before proceeding, make sure you have set up [Docker Desktop](https://docs.docker.com/desktop/) on your machine.

## Create directory structure

<!-- As many static-site generators render the title as Header1, I usually format the main headers as H2-->

To create the directories and set the structure for the project, in your project's root directory, run the following commands:

```bash
mkdir -p quickstart_docker/application
mkdir -p quickstart_docker/docker/application
```

<!-- Generally, I'd stick to the Google style guide, which suggests separating several commands with the initial '$'. However, many engineers find it more convenient to copy several commands to run them all at once and explicitly ask to omit this standard.  -->

As a result, you get the following directory structure:

```bash
quickstart_docker                # Root project directory
├── application                  # Code for the application goes here
└── docker                       # Directory for Docker-related files
    └── application              # Dockerfile for the application goes here
```

**Note**: a structured directory helps you organize files most effectively. But you can also place everything directly under the root directory.

## Create application file

To create the example application, do the following:

1. Go to the `quickstart_docker/application` directory:

    ```bash
    cd quickstart_docker/application
    ```

2. Run the following command to create the `application.py` file

    ```bash
    vi application.py
    ```

3. Paste the content in the file:

    ```python
    import http.server
    import socketserver

    PORT = 8800

    Handler = http.server.SimpleHTTPRequestHandler

    httpd = socketserver.TCPServer(("", PORT), Handler)

    print("serving at port", PORT)
    httpd.serve_forever()
    ```

    <!-- Is it 8000 or 8800, as the description states? I've changed everything to '8800', but I'd request clarification in real life. -->

    **Note**: the provided code sets up a basic HTTP server that serves files from the current directory on port 8800.
    
    You can check that the file is created correctly by running the command `cat application.py`.

## Set up Docker

You need Docker to run your application in a container. Since the application requires Python, an OS, and its dependencies, you can use a base image from Docker Hub. 

### Create Dockerfile

A **Dockerfile** is a text document containing instructions used by Docker to automate building a new container image.

To create a Dockerfile, do the following:

1. In the `quickstart_docker/docker/application` directory, create a file named **Dockerfile**:

    ```bash
    vi Dockerfile
    ```

2. Paste the following content inside **Dockerfile**:

    ```dockerfile
    # Use base image from the registry
    FROM python:3.5

    # Set the working directory to /app
    WORKDIR /app

    # Copy the 'application' directory contents into the container at /app
    COPY ./application /app

    # Make port 8800 available to the world outside this container
    EXPOSE 8800

    # Execute 'python /app/application.py' when container launches
    CMD ["python", "/app/application.py"]
    ```

### Build Docker image

A **Docker image** is a lightweight, stand-alone, executable software package that includes everything needed to run your application, including the code, runtime, system tools, system libraries, and settings.

To build the Docker image, do the following:

1. In the `quickstart_docker` directory, run the following command to build the Docker image:

    ```bash
    docker build . -f docker/application/Dockerfile -t exampleapp
    ```

    Here, the following arguments are used:

    - `.` specifies the current directory as the build context.
    - `-f docker/application/Dockerfile` specifies the Dockerfile's path.
    - `-t exampleapp` tags the built image with the name "exampleapp" to quickly identify it later.

    **Note**: to learn more about Docker image building, refer to the [Docker documentation](https://docs.docker.com/engine/reference/builder/).

2. To view the list of available Docker Images, run the following command:

    ```bash
    docker images
    ```

    You should see an output similar to the following:

    ```
    REPOSITORY  TAG       IMAGE ID       CREATED            SIZE
    exampleapp  latest    83wse0edc28a   2 seconds ago      153MB
    python      3.5       05sob8636w3f   6 weeks ago        153MB
    ```

Now, you can push the Docker image to a container registry and then deploy it to a [Kubernetes cluster](link-to-the-instruction).