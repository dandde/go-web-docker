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
