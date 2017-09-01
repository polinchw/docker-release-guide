# docker-release-guide
Guide to versioning and releasing Docker containers.

## Overview
This Git repo will serve as a guide for building and versioning an application in
with the goal of releasing it with Docker-Machine.

+ Diagram
+ Source code
+ Building, tagging, and releasing with Jenkins
+ Verify the releasing to Nexus
+ Verify the releasing to a Docker Repository
+ Controlling the deployment with Docker-Machine
+ Creating a Docker Swarm with Docker-Machine
+ Adding Portainer to view the Docker-Swarm(s)
+ Use Portainer to deploy application updates

## Process Diagram

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