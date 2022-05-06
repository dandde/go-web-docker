

```
$ docker run --publish 8080:8080 docker-gs-ping:multistage
```

* Run in detached mode

```
$ docker run -d -p 8080:8080 docker-gs-ping
```

* List container

```
$ docker ps
```

* Stop container

```
$ docker stop inspiring_ishizaka
```

 * When we ran the ```docker ps``` command, 
 * the default output is to only show running containers.
 * If we pass the ```--all``` or ```-a``` for short, 
 * we will see all containers on our system, 
 * that is stopped containers and running containers.

* To remove a container, 
* run the ```docker rm``` command passing the container name. 
* You can pass multiple container names to the command in one command.
```
$ docker rm inspiring_ishizaka 
```
* To name a container, we must pass the ```--name``` flag to the run command:
```
 docker run -d -p 8080:8080 --name rest-server docker-gs-ping
```
