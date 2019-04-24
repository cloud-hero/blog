+++
title = "Running GitLab-Runners on Kubernetes"
date = "2018-05-14T15:47:08+02:00"
tags = ["GitLab-Runners, Kubernetes, K8s"]
categories = ["programming"]
author = "Andrei Petrescu"

banner = "img/banners/banner-2.jpg"
+++

In the DevOps era where very much emphasis is placed on automation, having reliable, predictable and fast pipelines is a must. Fortunately, there are many options that you to try, like Jenkins, Buildbot, Drone, Concourse and so on. If you are trying to run jobs on k8s there is also the new Jenkins X available which brings major changes to Jenkins to run jobs in the cluster, but if you host your code on GitLab, you should use their CI/CD tool because it can save you a lot of time and money when done right.

 

The first step is to configure your projects to use the CI/CD pipeline offered by GitLab and after that you can start deploying. At first, you can use GitLab's free shared runners, but if you try to deploy your code at a bad time when the servers are overcrowded, you are in for a world of hurt. To makes things easier, GitLab enables you to run you jobs in your own cluster, and it is pretty simple to do so.

 

The first thing you need to do is go to your project, then to Settings --> CI/CD --> Runners settings and fetch the URL and the token to use when creating your runner. Keep in mind, that this is not the token that you will place in your configuration file, it is just a token that registers your runner, which in turn generates another token that is used to identify it.

 

The next step is running a gitlab-runner container locally to conigure you runner.



 

 

 

Once you are in the container run the following command:



 

 

It will open a prompt where it will ask for the URL and token that you copied earlier. It will ask you other things also, like adding tags to this runner, if you will allow untagged jobs to run on it, if you will lock this runner to this project and so on. The answers are yours to choose.

 

After finishing the registration, the command will generate a configuration file located in



This is the file that you will use when creating your runner pod on Kubernetes.

Store this configuration file in a ConfigMap like this:



Concurrent specifies the number of parallel jobs that the runner will run, and the privileged field should be set to true if you plan on using the docker:dind image as a service. If you do so, do not forget to set the DOCKER_HOST environment variable in your build container to tcp://localhost:2375 in your Settings --> CI/CD from your root directory where you can see all the projects.

 

After this you can pack the gitlab-runner into a deployment and mount the configmap like this:




Hope this helped you and happy deploying!

 

Andrei Petrescu,

DevOps Engineer at Cloudhero

 