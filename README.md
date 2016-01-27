
This demonstrates a sample web app to be deployed on [multicontainer Docker platform for Elastic Beanstalk](http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/create_deploy_docker_ecs.html)

## Prerequisites

* [boot2docker](http://boot2docker.io/)
* [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/)
* [AWS Account](http://aws.amazon.com/console/) 
* [AWS CLI](http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/eb-cli3-install.html).

## Steps

###Build Docker image
```sh
> git clone https://github.com/TraDuong1/eb-quickstart
> cd docker
# Log into a private Docker registry
> docker login <private_Docker_registry>
# Build the image
> docker build --no-cache=true --force-rm=true -t wildflywebapp .
# Push the image into private repository.
> docker tag 311a65c4dc55 <private_repo>/wildflywebapp
> docker push <private_repo>/wildflywebapp
# test the image
docker run -d --name webapp wildflywebapp
# verify webapp starts successfully 
>docker logs -f webapp
```

###Create Dockerrun.aws.json (v2)
```json
{
    "AWSEBDockerrunVersion": 2,
    "Authentication": {
        "Bucket": "<yourS3bucket>",
        "Key": "<yourS3key>"
    },
    "containerDefinitions": [{
        "name": "ebwebapp",
        "image": "<path/dockerimagename>",
        "essential": true,
        "memory": 512,
        "portMappings": [{
            "hostPort": 80,
            "containerPort": 8080
        }]
    }]
}
```

<pre>
AWSEBDockerrunVersion
Specifies the version number as the value "2" for multicontainer Docker environments.
Authentication
The location in Amazon S3 of a .dockercfg file that contains authentication data for a private repository 
containerDefinitions
	An array of container definitions
	name
	The name of the container.
	image
	The name of a Docker image in an private Docker repository 
	environment
	An array of environment variables to pass to the container. 
	essential
	True if the task should stop if the container fails.  
	portMappings
	Maps network ports on the container to ports on the host.
	links
	List of containers to link to. Linked containers can discover each other and communicate securely. 
</pre>

###Deploy Elastic Beanstalk application

* Use EB CLI to deploy and manage your Elastic Beanstalk applications and environments.
* Install EB CLI: http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/eb-cli3-install.html

```sh
# Configure the EB CLI
> eb init
# Create your first environment. If your project directory has source code in it, the EB CLI will bundle it up and deploy it to your environment
> eb create. 
# Check the current status of your environment
> eb status
# See a list of events output by Elastic Beanstalk
> eb events
# Once the environment is up and ready, you can update it 
> eb deploy
# if all goes well your app will be up on AWS
# http://<environment_url>/wildfly-helloworld/HelloWorld