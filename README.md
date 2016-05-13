## Advanced Docker

### File IO  
1. Build a new image from [Dockerfile](https://github.com/gsrajadh/Advanced-Docker/blob/master/FileIO/First_Container/Dockerfile):
```
docker build -t fileinput .
```

2. Create the first container which outputs to fileio.log:
```
docker run -td --name first_container fileinput
```

3. Create a second container using [Dockerfile](https://github.com/gsrajadh/Advanced-Docker/blob/master/FileIO/Second_Container/Dockerfile) which links to the first container:
```
docker build -t fileoutput .
docker run -td --link first_container:input --name second_container fileoutput
```

4. Access the file from the first container using curl command:
```
docker exec -it second_container bash
curl input:9001
```
Screencast Link: https://youtu.be/bIVl2jnT1f0

### Ambassador Pattern

1. Install docker-compose:
```
curl -L https://github.com/docker/compose/releases/download/1.5.1/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
```

2. There are two AWS instances. On the first instance the following two containers are running:  
  redis server  
  redis_ambassador_server which links to redis server  
  On the second instance the following two containers are running:  
  redis client  
  redis_ambassador_client which is used to send requests from redis client

3. On the first instance the following command will start the server:
  ```
  docker-compose up
  ```

4.  On the second instance the following command will start redis-cli:
  ```
  docker-compose run redis_client
  ```

5. Set and Get requests can be sent from the client to the server.

Screencast Link: https://youtu.be/J956wtFj3V0

### Green-Blue Deployment

1. The following command needs to be executed in the beginning to create a local registry:
```
docker run -d -p 5000:5000 --restart=always --name registry registry:2
```

2. [Post-commit](https://github.com/gsrajadh/Advanced-Docker/blob/master/Deploy/post-commit) hook has been configured such that:  
When git commit is done to the [App](https://github.com/CSC-DevOps/App) repository, it will build a new image, tag it and push it to the local registry.

3. Post-receive hooks have been configured for the following:
 * Pulling the image from the registry
 * Deploying the new docker container using that image or stopping an existing container and restarting it with the new code.  
 The above has been done for green and blue slices using the following post-receive hooks:    
 [Green post-receive](https://github.com/gsrajadh/Advanced-Docker/blob/master/Deploy/green_post-receive)  
 [Blue post-receive](https://github.com/gsrajadh/Advanced-Docker/blob/master/Deploy/blue_post-receive)

 Screencast Link: https://youtu.be/Lb5bxjlLvVo
