# Dockerizing a Django Web Application

Hey there, welcome! In this article, we will discuss the steps that details how to configure [Django](https://www.djangoproject.com/) to run on [Docker](https://www.docker.com/).

Docker gives you the ability to run your applications within a controlled environment, known as a container, built according to the instructions you define. A container leverages your machine's resources much like a traditional virtual machine (VM). However, containers differ greatly from traditional virtual machines in terms of system resources.

## Prerequisites

Before you begin this tutorial, ensure the following is installed to your system:

- [Python](https://www.python.org/) 3.7 or newer
- Python package manager ([pip](https://pypi.org/project/pip/))
- [Docker](https://www.docker.com/)

## Setting Up a Django web application

Let's jump directly to the application that we'll dockerize. We will be creating a Docker image for the django todo app that we have already created.

1. Go to the [django_todo_app](https://github.com/imnileshd/django_todo_app) repository.

2. Fork/Clone

3. Create and activate a virtual environment, check reference [link](https://packaging.python.org/guides/installing-using-pip-and-virtual-environments/):

    ```sh
    python -m venv env && .\env\Scripts\activate   
    ```

4. Install all the required dependencies for the project to run.

    ```sh
    (env) pip install -r requirements.txt
    ```

5. To initialize a local test database and get rid of the warnings run:

    ```sh
    python manage.py makemigrations
    python manage.py migrate
    ```

6. Run the app to verify that nothing is broken and the app runs without errors.

    ```sh
    (env) python manage.py runserver
    ```

## Creating a Dockerfile

A Dockerfile is a text file that contains instructions on how the Docker image will be built. A Dockerfile contains the directives below.

- `FROM`: Sets the base image from which the Docker container will be built.

- `WORKDIR`: Sets the working directory in the image created.

- `RUN`: Executes commands in the container.

- `COPY`: Copies files from the file system into the container.

- `CMD`: Sets the executable commands within the container.

In the root project directory, create a file with the name `Dockerfile` with no file extension and add the code below.

```docker
# pull the official base image
FROM python:3.8.3-alpine

# set environment variables
ENV PYTHONUNBUFFERED=1
ENV PYTHONDONTWRITEBYTECODE=1

# set work directory
WORKDIR /code

# install dependencies
COPY requirements.txt /code/
RUN pip install -r requirements.txt

# copy project
COPY . /code/

EXPOSE 8000

CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"]
```

`FROM python:3.8.3-alpine` sets the base image from which the Docker container will be created.

`ENV PYTHONUNBUFFERED=1` ensures that Python output is logged to the terminal, making it possible to monitor Django logs in realtime.

`ENV PYTHONDONTWRITEBYTECODE=1` prevents Python from copying pyc files to the container.

`WORKDIR /code` sets the working directory inside the container to /code.

`COPY requirements.txt /code/` copies the requirements.txt file into the work directory in the container.

`RUN pip install -r requirements.txt` installs all the required modules for the application to run in the container.

`COPY . /code/` copies all the project source code to the working directory in the container.

`EXPOSE 8000` exposes port 8000 for access from other applications.

`CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"]` sets the executable commands in the container.

> **_Note:_** Create `.dockerignore` file in the root project directory to reduce the image size by excluding files and folders that aren't needed such as `.git`, `.vscode`, and `__pycache__`.

## Building the Docker image

To build the Docker image from the `Dockerfile`, run the command below.

```sh
docker build --tag django_todo_app:latest .
```

`--tag` sets the tag for the image, e.g. `latest` in our image.

`.` indicates that the `Dockerfile` is within the current working directory.

To list all the images on your computer, execute the command below.

```sh
$ docker images

REPOSITORY        TAG            IMAGE ID       CREATED         SIZE
django_todo_app   latest         7fhgyvbjg1b9   1 hours ago     124MB
```

## Creating and running the Docker Container

To build and run a Docker container from the Docker image we created above, run the command below.

```sh
docker run --name django_todo_app -d -p 8000:8000 django_todo_app:latest
```

`--name` sets the name of the Docker container.

`-d` makes the image run in detached mode. The image is capable of running in the background.

`-p 8000:8000` maps port 8000 in the Docker container to port 8000 in localhost.

`django_todo_app: latest` specifies which image is used to build the Docker container.

To list all the running Docker containers, execute the command below.

```sh
docker ps
```

Navigate to `http://localhost:8000/` and confirm if the app is running in the container.

## Publishing the Docker image to Docker Hub

[Docker Hub](https://hub.docker.com/) is a repository of container images that can be used to create Docker containers.

To push docker image to the repository, executes below commands.

```sh
docker login

docker tag django_todo_app:latest <Docker ID>/django_todo_app:latest

docker push <Docker ID>/django_todo_app:latest
```

`docker login` command logs you into your Docker Hub account on the command line, making it possible to push Docker images to Docker Hub.

`docker tag django_todo_app: latest <Docker ID>/django_todo_app: latest` command modifies the tag of our local Docker image to include the Docker ID.

`docker push <Docker ID>/django_todo_app: latest` pushes the image to Docker Hub repository we created earlier.

Visit Docker Hub and create an account if you don't have one.

## Conclusion

In this article, we walked through how to containerize a Django web application. I hope this was helpful to you.

You can refer to the official [Docker documentation](https://docs.docker.com/get-started/#docker-concepts). It explains in depth every thing about Docker you need to know.

You can find the code in the [dockerize_django_app](https://github.com/imnileshd/dockerize_django_app) repo.

Thanks for reading!
