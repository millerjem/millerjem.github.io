---
layout: default
title: CICD Lab
nav_order: 3
has_children: true
---

# CICD Lab
This lab shows you how to deploy a CI/CD pipeline using Jenkins in DC/OS

By the end of this lab series, you will have configured Jenkins to:

* Monitor a GitHub repository for new commits,
* Pull the code from GitHub when Jenkins discovers new commits,
* Build the new code into a Docker container,
* Push the new container to DockerHub, and
* Deploy the new application!

### Prerequisites
In order to complete these lab exercises, please ensure you have the following:

* GitHub account and access to your credentials
* DockerHub account and access to your credentials
* DC/OS cluster
