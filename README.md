# docker-release-guide
Guide to versioning and releasing Docker containers.

## Overview
This Git repo will serve as a guide for building and versioning an application in
with the goal of releasing it with Docker-Machine.

+ Video
+ Process Diagram
+ Source code
+ Building, tagging, and releasing with Jenkins
+ Verify the release to Nexus
+ Verify the release to a Docker Registry
+ Creating a Docker Swarm with Docker-Machine
+ Controlling the deployment with Docker-Machine
+ Adding Portainer to view the Docker-Swarm(s)
+ Use Portainer to deploy application updates

## Video

https://www.youtube.com/watch?v=FTmYFLxsDP0


## Process Diagram

![alt text](https://raw.githubusercontent.com/polinchw/docker-release-guide/60d9607c3fb6b8430e1ba3496b975f044f8171f3/diagrams/process.png)

## Source Code

Your source code will be the most important part of your project.  You'll want it kept in a source
code repository such as Git, SVN, or CVS.  When you deploy your project to test or production you'll 
want a tag to back up what you're releasing.  This tag will help you test a copy of code that was 
released if a bug is reported and it can help you compare changes to other releases.  A tag can even 
even help you roll your code back to a previous version if something gets merged into the trunk that 
you no longer want there.

This guide will use an app that is commited to GitHub as an example of how to put something through 
the release process.

![Sample App on GitHub](https://github.com/polinchw/monitorserver)

This sample app is a Java application that will get tagged, versioned, and bundled up as a Docker image
so that it can be release.  The way that the tagging and versioning is done will be specific to Java 
in this example, however how one would release the end product Docker image is programming language 
agnostic.

## Building, tagging, and releasing with Jenkins

Jenkins is a continous integration server that can build your app.  For this example I've set up
a Jenkins server on AWS to build with.

I'm using Jenkins to carry out these tasks:

+ Check out the code from GitHub
+ Run the Maven Release-Plug-In which will:
+ Create a release number by dropping the SNAPSHOT version number from the Maven pom.xml
+ Tag the code in GitHub
+ Compile the code and build the jar artifact
+ Push the jar artifact to Nexus
+ Build a Docker image with the same build version the jar is using
+ Push the docker build to Docker Hub
+ Bump the Maven version number and re-add the SNAPSHOT
+ Commit the code back to GitHub with the new SNAPSHOT number

Once Jenkins has successfully run all of these steps you will have produced a tagged and versioned release.

 ## Verify the release to Nexus
 
 Nexus is a binary repository.  It will store the versioned jar file that was created by Jenkins in
 the previous section. Nexus can be used by a software development team to share libraries that the
 team creates.  It can also pull in and cache all the 3rd party dependencies that your code needs to build.
 
 We should check that Nexus has stored the build that was produced by Jenkins. 
 
 ## Verify the release to a Docker Registry
 
 We want to use a Docker registry to store our docker builds.  I'm using Docker Hub to store my docker
 builds in this example.  You can set up your own Docker registry if your organization needs it.  
 
 We should check that Docker Hub has recorded the new versioned build.
 
 ## Creating a Docker Swarm with Docker-Machine
 
 Docker-Machine is an application that can remotely control servers that have Docker installed.  I'm using
 Docker-Machine to group servers into a Docker Swarm then I'm deploying my app with it.
 
 I have a script for creating a Docker Swarm on AWS with Docker-Machine.
 
 ![Bash script for creating Docker Swarms on AWS with Docker-Machine](https://github.com/polinchw/docker-tools)
 
 ## Controlling the deployment with Docker-Machine
 
 Once the Docker Swarm is up and running you can deploy the Docker image that was built to it.  This is an example 
 of how to run the container as a Docker Service using Docker-Machine:
 
 docker-machine ssh monitorserver-swarm-master 'docker service create -e "SPRING_PROFILES_ACTIVE=aws-dev" -e "JASYPT_ENCRYPTOR_PASSWORD=xxx" --replicas 2 --name monitor-server -p 8080:8080 polinchw/monitor-server'
 
 ## Adding Portainer to view the Docker-Swarm(s)
 
 Docker-Machine is great but it's command line driven.  You can add a pretty GUI front end to Docker-Machine by installing
 Portainer.  In my example I'm running Portainer on the same Linux VM that is running Docker-Machine.
 
 ## Use Portainer to deploy application updates
 
 Once you have your app released with Docker-Machine or Portainer (yes you can release your Docker Service right from Portainer) 
 you can use Portainer to update your app by running another build through Jenkins again then simply updating the 
 Docker Service number in Portainer.  
 
 Portainer would be great for a Dev-Ops group that is responsible for pushing out releases.    
 