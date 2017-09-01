# docker-release-guide
Guide to versioning and releasing Docker containers.

## Overview
This Git repo will serve as a guide for building and versioning an application in
with the goal of releasing it with Docker-Machine.

+ Source code
+ Building, tagging, and releasing the code with Jenkins
+ Tagging the Git repo
+ Releasing to Nexus
+ Releasing to a Docker Repository
+ Controlling the deployment with Docker-Machine
+ Creating a Docker Swarm with Docker-Machine
+ Adding Portainer to view the Docker-Swarm(s)
+ Use Portainer to deploy application updates

## Source Code

Your source code will be the most important part of your project.  You'll want it kept in a source
code repository such as Git, SVN, or CVS.  When you deploy your project to test or production you'll 
want a tag to back up what you're releasing.  This tag will help you test a copy of code that was 
released if a bug is reported and it can help you compare changes to other releases.  A tag can even 
even help you roll your code back to a previous version if something gets merged into the trunk that 
you no longer want there.

This guide will use an app that is commited to GitHub as an example of how to put something through 
the release process.

### Example App

![alt text](https://github.com/polinchw/monitorserver)