
## YouTube Link
For the full 1 hour course watch on youtube:
https://www.youtube.com/watch?v=6YZvp2GwT0A

# Installation
## Build the Jenkins BlueOcean Docker Image (or pull and use the one I built)
```
docker build -t myjenkins-blueocean:2.414.2 .

#IF you are having problems building the image yourself, you can pull from my registry (It is version 2.332.3-1 though, the original from the video)

docker pull devopsjourney1/jenkins-blueocean:2.332.3-1 && docker tag devopsjourney1/jenkins-blueocean:2.332.3-1 myjenkins-blueocean:2.332.3-1
```

## Create the network 'jenkins'
```
docker network create jenkins
```

## Run the Container
### MacOS / Linux
```
docker run --name jenkins-blueocean --restart=on-failure --detach \
  --network jenkins --env DOCKER_HOST=tcp://docker:2376 \
  --env DOCKER_CERT_PATH=/certs/client --env DOCKER_TLS_VERIFY=1 \
  --publish 8080:8080 --publish 50000:50000 \
  --volume jenkins-data:/var/jenkins_home \
  --volume jenkins-docker-certs:/certs/client:ro \
  myjenkins-blueocean:2.414.2
```

### Windows
```
docker run --name jenkins-blueocean --restart=on-failure --detach `
  --network jenkins --env DOCKER_HOST=tcp://docker:2376 `
  --env DOCKER_CERT_PATH=/certs/client --env DOCKER_TLS_VERIFY=1 `
  --volume jenkins-data:/var/jenkins_home `
  --volume jenkins-docker-certs:/certs/client:ro `
  --publish 8080:8080 --publish 50000:50000 myjenkins-blueocean:2.414.2
```


## Get the Password
```
docker exec jenkins-blueocean cat /var/jenkins_home/secrets/initialAdminPassword
```

## Connect to the Jenkins
```
https://localhost:8080/
```

## Use Docker Container's Bash Shell
```
docker exec -it jenkins-blueocean bash
```

## Installation Reference:
https://www.jenkins.io/doc/book/installing/docker/


## alpine/socat container to forward traffic from Jenkins to Docker Desktop on Host Machine

https://stackoverflow.com/questions/47709208/how-to-find-docker-host-uri-to-be-used-in-jenkins-docker-plugin

> docker run -d --restart=always -p 127.0.0.1:2376:2375 --network jenkins -v /var/run/docker.sock:/var/run/docker.sock alpine/socat tcp-listen:2375,fork,reuseaddr unix-connect:/var/run/docker.sock

To get Container IP Address:
> docker inspect <container_id> | grep IPAddress

OR
> docker inspect <container_name> | grep IPAddress

To get TCP port:
> docker inspect eloquent_williamson | grep tcp

## Connecting to Docker container from Jenkins as a cloud agent

> Put Docker Host URI as : tcp://172.19.0.3:2375

## Creating a Docker Agent Template

> Name: docker-agent-apline
> labels: docker-agent-alpine
> Docker Image: jenkins/jnlp-agent-python
> Instance Capacity: 2
> Remote File System Root: /home/jenkins

## Using my Jenkins Python Agent
```
docker pull devopsjourney1/myjenkinsagents:python
```

## Using jenkins agent in a job

go to job configuration -> restrict where this project can be run -> label expression -> docker-agent-alpine
Now run the job again

# Running Jenkins jobs automatically

- Go to job configuration -> Build Triggers -> Poll SCM -> Schedule: * /5 * * * * (put spaces around *, every 5 minutes check for changes in the repo)

# Pipelines

- We can use 'jenkins' file with jenkins Script writtin in groovy language
- or we can write the script in the job configuration itself.

- the pipeline stages might fali if it enounters error or stage above it fails.
