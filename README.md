# go-web-docker

* Create the GitHub Repository
* Use the Clone or download button to get your repo URL:
```
$ git clone YOUR_REPOSITORY_URL
```

* Clone the repository to your machine:
```
$ git clone YOUR_REPOSITORY_URL
```
* We can use the repository name to initialize the project:
```
$ mkdir src
$ cd src
$ export GOFLAGS=-mod=vendor
$ export GO111MODULE=on
$ go mod init github.com/YOUR_GITHUB_USER/YOUR_REPOSITORY_NAME 
# (example: go mod init github.com/tomfern/go-web-docker)
```
* From now on, we can use these commands:
```
$ go mod download
$ go mod vendor
$ go mod verify
```
* To download the required dependencies in the vendor/ directory, 
* much easier than downloading the modules one by one manually. 
* easier later when we set up Continuous Integration.

### Application File Contents

* Before continuing, let’s create the file structure:
```
$ mkdir conf views
```

* The main application file ```main.go``` 
* test file ```main_test.go```
* The content of ```views/result.html```
* The content of ```views/invalid-route.html```

### Configuration File Contents

The ```conf/app.conf``` file is read by Beego to configure the application. Its content is as follows:
```
appname = mathapp
runmode = "dev"
httpport = 8010
```

* In this file:

    **appname**: is the name of the process that the application will run as,
    **httpport**: is the port on which the application will be served, and
    **runmode**: specifies which mode the application should run in. Valid values include dev for development and prod for production.
* Finally, install the Go modules with:
```
$ go mod download (go mod tidy)
$ go mod vendor
$ go mod verify
```

### Configuring Docker for Development

* **Step 1** – Creating the Dockerfile
  - Go back to the top level of your project:
```
$ cd ..
```
* The following Dockerfile should satisfy the above requirements.

```
FROM golang:1.15.7-buster
RUN go get -u github.com/beego/bee
ENV GO111MODULE=on
ENV GOFLAGS=-mod=vendor
ENV APP_USER app
ENV APP_HOME /go/src/mathapp
ARG GROUP_ID
ARG USER_ID
RUN groupadd --gid $GROUP_ID app && useradd -m -l --uid $USER_ID --gid $GROUP_ID $APP_USER
RUN mkdir -p $APP_HOME && chown -R $APP_USER:$APP_USER $APP_HOME
USER $APP_USER
WORKDIR $APP_HOME
EXPOSE 8010
CMD ["bee", "run"]
```

* The first line:
```
FROM golang:1.15.7-buster
```
* References the official image for Go as the base image. This image comes with Go 1.15 pre-installed.

* The second line:
```
RUN go get -u github.com/beego/bee
```
* Installs the bee tool globally (Docker commands run as root by default), which will be used to live-reload our code during development.

* Next, we configure the environment variables for Go:
```
ENV GO111MODULE=on
ENV GOFLAGS=-mod=vendor
```
* There are two types of variables:

    ARG: these take effect at build time, we must set these values when we create the image.
```
ARG GROUP_ID
ARG USER_ID
```
    ENV: define run-time variables, these are activated when the container is started.
```
ENV APP_USER app
ENV APP_HOME /go/src/mathapp
```
* The next lines:
```
RUN groupadd --gid $GROUP_ID app && useradd -m -l --uid $USER_ID --gid $GROUP_ID $APP_USER
RUN mkdir -p $APP_HOME && chown -R $APP_USER:$APP_USER $APP_HOME
```
```
USER $APP_USER
WORKDIR $APP_HOME
```

* Creates a user called app, a home directory and an app directory inside the container.

* The next to last line:
```
EXPOSE 8010
```
* Tells Docker that port 8010 is interesting.

* The final line:
```
CMD ["bee", "run"]
```
* Uses the bee command to start our application.

**Step 2** – Building the Image

* Once the Docker file is created, run the following command to create the image:
```
$ docker build \
         --build-arg USER_ID=$(id -u) \
         --build-arg GROUP_ID=$(id -g) \
         -t mathapp-development .
```

* Executing the above command will create an image named mathapp:
```
    –build-arg: sets a build time variable. We’ll use it to make the user and group IDs in your machine and the container match.
    -t mathapp: sets the tag name for the new image, we can reference the image later as mathapp:latest
 
* Don’t forget to type the last dot (.) in the command.

This command can be used by everyone working on this application. This will ensure that an identical development environment is used across the team.

To see the list of images on your system, run the following command:
``
$ docker images
```
Note that the exact names and number of images might vary. However, you should see at least the golang and mathapp images in the list:

REPOSITORY               TAG            IMAGE ID            CREATED                 SIZE
golang                   1.15           25c4671a1478        2 weeks ago             809MB
mathapp-development      latest         8ae092824585        60 seconds ago          838MB

Step 3 – Running the Container

Once you have mathapp, you can start a container with:

$ docker run -it --rm -p 8010:8010 -v $PWD/src:/go/src/mathapp mathapp-development

Let’s break down the above command to see what it does.

    The docker run command is used to run a container from an image,
    The -it flag starts the container in an interactive mode (tie it to the current shell),
    The --rm flag cleans out the container after it shuts down,
    The --name mathapp-instance names the container mathapp-instance,
    The -p 8010:8010 flag allows the container to be accessed at port 8010,
    The -v $PWD/src:/go/src/mathapp is more involved. It maps the src/ directory from the machine to /go/src/mathapp in the container. This makes the development files available inside and outside the container, and
    The mathapp part specifies the image name to use in the container.

