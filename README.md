
Sample web deployed on [multicontainer Docker platform for Elastic Beanstalk](http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/create_deploy_docker_ecs.html)

## Prerequisites

* [boot2docker](http://boot2docker.io/)
* [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/)
* [AWS Account](http://aws.amazon.com/console/) 
* [AWS CLI](http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/eb-cli3-install.html).

## Steps

###Build Docker image
```sh
> git clone https://github.com/TraDuong1/eb-quickstart
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
```javascript
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