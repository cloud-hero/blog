+++
title = "Best Practice Approach to Application Logging in Kubernetes"
date = "2019-04-05T15:47:08+02:00"
tags = ["Application, Logging, Kubernetes"]
categories = ["programming"]
author = "Andrei Petrescu"

banner = "/images/kubernetes1.png"
+++
Hey guys,

Today I am going to talk about the EFK stack, what it is, and how you can configure it for maximum visibility in your logs.
    The 3 components of the EFK stack are as follows:
    - Elasticsearch
    - Fluentbit/Fluentd
    - Kibana

    In this blog post I will focus on Fluentbit, as it is the lightweight version of Fluentd and, in my opinion, is more suited to Kubernetes. We will also focus on how I reached this the final solution and the hurdles I had to overcome, and also how to handle application logs without actually installing and 3rd party clients like https://github.com/fluent/fluent-logger-java.

    So, first off, the Fluentbit is the agent which tails your logs, parses them, and sends them to Elasticsearch. In turn, Elasticsearch stores them in different (or the same) indexes, and provides an easy and powerful way to query your data. To make visualization easier, we have Kibana, which is a UI over Elasticsearch and helps you query your data either by using the Lucene query syntax, or by clicking on certain values for certain labels (e.q. you want all logs that have the log_level set to ERROR).

    The simplest way to start off with creating this stack, is to deploy all components using helm charts. If you do not know what Helm or Helm charts, I will try to briefly explain the two. Helm is a package manager for Kubernetes, sort of how APT is a package manager for Debian, and Helm charts are packages which describe different software components (databases, caches, etc.). A Helm chart is composed of two main parts:
    - Templates
    - Values

    This abstraction helps us get rid of duplicate code, and store only what changes from environment to environment in the values file. When you download a chart, you only need to change the values file to suit your needs (e.g. maybe to want to change the size of the PVC that will be created, or you don't want a PVC, or you want to change the resource limits on your containers, the sky is the limit).
    
    For the deployment of Elasticsearch and Kibana, I am not going to use Helm, as I want to deploy a single-node Elasticsearch, and the Kubernetes cluster which I will be using shall be the one provided by Docker for Desktop. Fluentbit will be installed by using its Helm chart.