	# The Docker Book

Things to checkout
Shipyard, DockerUI, maDocker - UI's for docker


## Docker commands:
- docker help							
- docker info                                       - get info from the server about current images and containers on the system
- docker-machine 						            - tool to manage the virtual machine on mac osx. MUST NOT BE RUN AS SUDO
- docker-machine ip     							- get ip address of the server (different depending if your running in windows/mac or linux)
- docker-machine status 							- check if docker is running
- docker-machine ls 								- get a list of all docker machines
- docker machine create --driver virtualbox \<NAME> - create a new docker machine using the virtualbox driver with the provided name
- eval "$(docker-machine env <NAME>)" 				- setup the current consule to use the docker machine provided by NAME
- docker ps -a 										- show all containers including ones that have stopped (-l option will show the last container) - it will also show network and port mapping information
- docker run -i -t \<IMAGE>:\<TAG> \<COMMAND> 		- create and start a new container in interactive mode based off the ubuntu image 
- docker run --name \<NAME> ...						- create and start a new container with a certain name (needs to be unique)
- docker run --name \<NAME> -d 						- create and start in daemon mode (best for services and long running processes) - for background tasks. STDOUT doesnt goto terminal
- docker run -p \<PORT> ...							- run a container and map the exposed port in the container to a port defined <PORT>. if no port is provided at run then one is randomly selected between 49000- 49900
- docker run -p \<LOCAL-PORT>:\<CONTAINER-PORT>		- run container and map a local port to the containers exposed port ie. -p 8080:80
- docker run -P ...									- expose all container ports and local ports
- docker run -v \<HOST_DIR>:\<CONTAINER_DIR>		- run the conatiner and mount a local directory to a directory in the container
- docker run -v \<xxx>:\<yyy>:ro/rw 				- Mount the volume with either read only or read write permissions
- docker run --link \<CONTAINER_NAME>:\<ALIAS>		- run a container and link it directly to another container so that it could see it. give the connection an alias
- docker run -h \<HOSTNAME>							- run a container and set the hostname, this will end up in the hosts file
- docker run --volumes-from \<CHILD_NAME>
- docker port \<ID> \<CONF-EXPOSED-PORT>			- show what real port is being mapped to the exposed port
- docker start \<NAME/ID>							- start a container that already exists - it will start with the same options it was created with
- docker stop \<NAME/ID>							- stops a running/daemonized container - sends the standard SIGTERM signal
- docker kill \<NAME/ID>							- kills a running/daemonized container - sends the standard SIGKILL signal
- docker attach \<NAME/ID>							- connect to the docker container that is currently running
- docker logs -f \<NAME>							- view the output of a container through its logs. You can use the optional -f flag to tail the logs
- docker inspect \<NAME>							- get details about a container
- docker inspect -f "{{ .Volumes }}" \<NAME>		- get details on where the volumes are actually on disk
- docker rm \<ID>									- delete a container. The container must not be running in order to delete it
- docker rmi \<IMAGE>								- delete an image locally
- docker images 									- list images available locally
- docker image \<USER>/\<REPO>						- list images in a repo
- docker pull \<IMAGE>:\<TAG>						- download images from the hub
- docker search \<IMAGE>							- do a search against the docker hub for an image
- docker login										- login using your hub account (the id is NOT your email address)
- docker commit \<ID/NAME> \<USER>/\<REPO>			- do a local commit of an image
- docker commit .... -m '' -a 'author'
- docker push \<USER>/\<REPO>						- push a locally commited image to the docker hub
- docker build .									- build the docker file in the current directory and give the image the latest tag
- docker build -t="\<USERNAME>/\<IMAGENAME>:\<VERISON>" .	- build the docker image from the current directory and give it a tag
- docker history \<ID>								- look at each layer of the image to see how it was built
- docker-compose up									- use the current directories docker-compose.yml file to start up containers defined in the file
- docker-compose up -d								- run in detach mode
- docker-compose down								- stop all the containers
- docker-compose -f \<FILENAME\> up/down			- docker compose with a defined file
- docker swarm init --advertise-addr <MANAGER-IP>	- start a swarm with the defined ip as the manager
- docker swarm join-token worker 					- generate the token for a worker to join the swarm
- docker swarm join --token  <TOCKEN> <IP:PORT>    	- join the swarm
- docker swarm leave \<NODE\>						- remove a worker node from the swarm
- docker info 										- get the general state of the swarm
- docker node ls 									- list all nodes in a docker swarm
- docker node ps \<NODE-NAME\>						- list running containers on a specific node
- docker node inpect \<NODE-NAME\>					- print out node information
- docker node promote/demote \<NODE-NAME\>			- promote or demote a node to/from manager
- docker node remove \<NODE-NAME\>					- remove a node from a swarm
- docker service create --replicas <NO> --name <NAME> <IMAGE> 	- create a service from the manager and allow the manager to choose where to deploy the image
- docker service ps <NAME>							- looks for all services with name in the swarm

## Installation
- On a modern linux machine you don't need much. For latest Ubunutu, you could get away with configuring the repo then 'sudo apt-get install lxc-docker'
- Docker requires root level permissions when running, so most if not all commands will require sudo
- When installing on ubuntu based systems, you need to configure the Uncomplicated Firewall (UFW) to get it working with a network bridge. This is because UFW drop forwarded packets.
-- To do so, edit the /etc/default/ufw file and update the property "DEFAULT_FORWARD_POLICY" to "ACCEPT" then reload with 'sudo ufw reload'
- Windows and Mac OSX requires boot2docker to run docker
- boot2docker works by running docker server and containers in a tiny virtual machine (requires virtualbox to work)
- As docker containers usually run locally (on unix systems) you would normally connect to them using localhost but for windows and mac, as they are in a VM you will need to connect to the containers via the VM's IP address.
-- To get the IP address of the VM just start the docker app and it will print it out or use the command 'docker-machine ip'


## Working with containers
- To start a new container, use the command: 'docker run -i -t \<IMAGE> \<COMMAND>' i.e. docker run -i -t ubuntu /bin/bash. -i makes STDIN open to the container, -t assign a pseudo tty to the container.
- This command will look to a locally install image (ubuntu), if it doesn't find it, it will download it from the docker hub.
- Then it will run the command (/bin/bash) within it
- If the container doesn't exist yet, it will create it for you before running it.
- It will also setup networking, filesystem, ID, name and bridge interface to talk to the host
- You will have a command prompt for the container, once you exit it, the container will stop running
- Using the 'docker ps -a' command will show you all the containers
- If you create a new container without a name, docker will provide one for you unless you provide one with the --name option i.e. sudo docker -run --name my_container -i -t.
- Most docker commands can be run with a name rather than an id.
- Container names need to be unique
- To start a container that exists but has stopped, use the command 'sudo docker start \<NAME/ID>' i.e sudo docker start my_container
- Starting a container this way will start it like we did with the run command (in this case at the bash prompt)
- To reattach to this container in interative mode use the attach commdn 'sudo docker attach \<NAME/ID>'
- You can create and run daemonized containers with the -d option. These are best suited for service type applications. 'sudo docker run --name server -d ubuntu /bin/bash...'
- Running in daemon mode stops the output from going to the terminal, to see whats happening in a container use the comand 'sudo docker logs \<NAME>' you can use it with the '-f' flag to tail and '--tail 0 -f' to tail without going through the entire log
- You can look at the processes in a container with the 'sudo docker top \<NAME>'
- You can stop a container with the 'sudo docker stop \<NAME/ID>' or 'sudo docker kill \<NAME/ID>' these will send SIGTERM or SIGKILL signals to the container
- Getting details about a container can be done using: 'sudo docker inspect \<NAME>'
- docker stores images, containers and container configuration under the /var/lib/docker directory
- To delete containers use the following command: 'docker rm \<ID>'. The container must not be running to delete it.
- docker run -h \<HOSTNAME\> runs a container and sets the hostname of the contain to \<HOSTNAME\> 
- docker -e "\<ENV\>=\<VALUE\>" set environment variables to be used within the docker file

## Docker Images and Containers

- Docker uses a filesystem called the union file system
- union filesystems allow you to mount multiple systems and make it look as though its one file system.
- The docker file system consists of layers upon layers of file systems with the top most one being the one where you keep your changes
- Each layer is read only with the exception of the top most one
- The lowest FS is the one that contains the kernal and bootfs, then on top of that its the base image i.e. ubuntu, the a number of other layers can be on top of that
- As every layer is read only, any change to files in these file systems will result in a copy of the file into the writable layer than the changes are saved there. this is called "Copy on Write". This makes everything read only / static and changes that you want made only appear in the writable layer and is refered to
- Images are stored in repositories and repositories are stored in the docker hub.
- When using an image, you should always refer to it by the image name and its tag
- There are two types of repositories, top level ones which are maintained by docker and vendors i.e. redhat and ubuntu, then theres third party ones run by normal people.
- A user repository takes the form for \<USERNAME>/\<REPO>
- docker images can be downloaded manually using the command 'docker pull \<IMAGE>'
- and you can search for images to download using the command: 'docker search \<IMAGE>'
- There are two ways in which you can build your own images, use the 'docker commit' or 'docker build' with a build file
- Its generally recommended to use the build command with build file as its more flexiable and 'powerful'
- To use 'docker commit' you would first need to log into your account on the command line with 'docker login'. This will then store your configuration in .docker .config
- To use 'docker commit' you would need to customise a running container i.e. install apache. Then exit and the use the 'docker commit \<IMAGEID> \<USER>/\<REPO>' command
- The 'docker build' command is the recommneded way of building docker images, it uses a docker file that uses DSL instructions to build the image
- The directory containing the dockerfile is called the build environment aka build context.
- The build context and its contents are uploaded to the docker daemon to build docker images, so that it has access to all files etc
- Example of a docker file

```
#Docker file version 1 - comment
FROM ubuntu:14.04 #this should always be the first instruction. this is known as the base image
MAINTAINER Paul hoang
ENV REFRESHED_AT 01-01-2016
RUN apt-get update
RUN apt-get install -y nginx
RUN echo 'Hi this is the conatiner' > /usr/share/nginx/html/index.html
EXPOSE 80 #This tell the daemon that port 80 is used to communicate with this container, this doesn automatically open it though, thats up to the run command to specify
```

- Each instruction is in upper case follwed by an argument
- Each instruction adds a new layer to the image and commits
- If an instruction fails, since the last instruction committed, you can run the container and debug why it didnt work
- To do this get the ID of the image from the build output and use it with docker run i.e.

Step 3: RUN apt-get update
--->Running in \<PREVIOUS-IMAGE-ID>
--->\<NEW IMAGE ID>
Step 4: failed

- docker run -t -i \<NEW IMAGE ID> /bin/bash
... now debug

- To build the image, you must be within the build context and run the command 'docker build -t="\<REPO>/\<IMAGENAME>:\<VERISON>" .' the dot is to look at current dir for docker file
- You can also you a git repo instead of the current directory as the source of the docker file 'docker build git@github:\<USER>/\<REPONAMW>'
- .dockerIgnore file is used as a list like .gitIgnore to ignore files to send to the daemon
- As each instruction is committed, it could be used as a cache if rerun. This causes issues if you want to make changes. if you want to clear the cache i.e you want to rerun the apt-get update step, one could build with the --no-cache option i.e. 'docker build --no-cache'
- Anoher way to clear the cache is to use the ENV instruction. If this changes and you rebuild, it will clear the cache of all the instructions following the ENV instruction

##Docker file instructions
- CMD ["\<COMMAND>","ARG1 To Command", "Arg2"]	
	- specify a command to run in the container (much like the command defined when using docker run). its recommended to use the array syntax for this instruction. the docker run command overrides this instruction if a command is provided at command line

- ENTRYPOINT 
	- like the CMD instruction but the commands provided during the docker run command is provided to the ENTRYPOINT intruction i.e. ENTRYPOINT ["apt-get install apache2"] and if we run the docker container with the following - docker run -t -i \<CONTAINERNAME> -y. This will provide ENTRYPOINT with an additional argument -y
	- ENTRYPOINT can also be overridden with the --entrypoint option
	- When using an entrypoint that requires options from the command line, its always a good idea to also provide a CMD instruction to show the help options if nothing is provided i.e.

```
ENTRYPOINT ["wget"]
CMD ["-?"]
```

- WORKDIR this instruction defines the working directory for all instructions after it i.e.

```
WORKDIR /var/log/tomcat7
CMD ["cp", "./console.out", "/home"] 
WORKDIR /opt/apache2/www/html
...
```

- ENV 
	- sets environment variables i.e. ENV JAVA_VERSION 1.8.
	- you can also provide environments via the command line with the -e flag i.e. docker run -ti -e "JAVA_VERSION=1.8" \<CONTAINER_NAME> \<CMD>

- USER
 	- this instruction provides the container the user name in which the image should run as
	- username or UUID can be provided here
	- the -u flag can be used to provide a user during runtime
	- the default user it root !!

- VOLUME
	- adds a volume to one or more containers
	- used as persistent or shared storage
	- used for things such as source code or db
	- i.e. VOLUME ["/opt/project"]
	- a volume can be shared even though the container sharing it isn't running
	- removing a container will only remove the volumne once all the containers using it are removed
	- Does NOT remove the content of a directory inside the container if it already exists. Once mounted to the host directory, it will overlay its contents


- ADD
	- copy files or directories from the build environment to the image
	- supports urls
	- support .tar files and automatically unpacks them for you
	- if destination folder doesn't exist, docker will create it with 0755 and UID of 0
	- i.e. ADD apache.conf /etc/apache2/apache.conf

- COPY
	- like add but focused on copy files from the BUILD CONTEXT (the dir in which the docker build file lives)
	- no support for extracting files

- ON BUILD
	- creates triggers to images
	- used when an image is used as a base for another image
	- it essentially adds new instructions to the image using this image by additing them straight after the FROM intruction
	- i.e. ONBUILD ADD . ./badsdfds/sdfsdf


## Docker hub
- You can save docker images to the docker hub with the push command i.e. sudo docker push \<USER>/\<IMAGE_NAME>
- Automated builds are possible by connecting github or bitbucket to dockerhub and push the docker file to the github/bitbucket repo
- Removing images locally is done with the rmi command. i.e. sudo docker rmi \<USER>/\<IMAGE_NAME>
- Removing from the docker hub is hub via the web interface
- You can run your own docker register by using a image provided by docker or you can use a service like quay.io
- To run the standard docker provided registry, use command: sudo docker run -p 5000:5000 regristry
   - Tag an image using its id with: docker images
   - docker tag \<IMAGE-ID> \<REGISTRY-DOMAIN-NAME>:5000/\<USERNAME>/\<IMAGENAME>
   - docker push \<REGISTRY-DOMAIN-NAME>:5000/\<USERNAME>/\<IMAGENAME>

## Using Docker
- To mount a volume in your container, use the -v flag during run: docker run -ti -v \<HOST_DIR>:\<CONTAINER_DIR> ...
- where the host directory is the directory on the host machine you wish to mount
- where container directory is the directory to mount the host directory to
- You can connect containers in two ways.
	- Use the docker networking stack
		- This method involves connecting using the ip address that docker assigns to each container using its virutal interfaces (not recommended as these IP's change when the container restarts and it hard codes the ips in the parent container)
	- Use the in built link method
		- Use: sudo docker run -d --name myDatabasContainer \<DB_IMAGE>
		- Then: sudo docker run -ti --name myWebApp --link myDatabaseConatiner:dbConnection \<WEBAPP_IMAGE>
		- Prefered method, doesn't require you to expose the port of the child container (this case the DB) using the -p flag, exposed ports from the docker file are only exposed to the parent container
		- Allows multuple containers to be linked this way
		- Security benefit as the container isn't accessable from outside the host
		- As an additional security option is to use the --icc=false flag, this will force the child container to only allow comms between containers using links
		- Docker populates the link alias into the parent's /etc/hosts file and ENV variables i.e you will see xxx.xxx.xxx.xxx dbConnection in the hosts file. To view the environment variables, use the "env" command in a container.
- Sharing volumes between containers can be done using the --volumes-from <NAME> flag during run. This will allow the running container access to a VOLUME exposed in the child container.
	- You will need to define in child container a VOLUME instruction or at least run the child container with the -v flag 
	- This will work even if the child container isn't running
	- Deleting the child container will only delete the volume if all containers using the volume are deleted.
	- To backup volumes, you can run a new container that backups the volume then removes itself. i.e. docker run -rm --volumes-from \<CHILD_CONTAINER> -v $(pwd):/\<DIR_TO_BK_TO> ubuntu tar -zcf /\<DIR_TO_BK_TO>.tar.gz /\<WHATEVER_YOU_WANT_TO_BACKUP>
- You can get ssh like access to any container using the command line too nsenter. Its available via a docker container
	- To install nsenter run: docker run -v /usr/local/bin:/target jpetazzo/nsenter
	- To run nsenter you need the process id PID of the container you wish to enter
	- docker inspect "{{ .State.Pid }}" \<CONTAINER>
	- sudo nsenter --target \<PID> --mount --uts --net --pid
- You can interact with docker using an API directly. (its restful)
	- Registry API
	- Docker Hub API
	- Docker Remote API 
 
 ## Docker swarm


