---
layout: post
title:  "Making Kubernetes Easier with DigitalOcean Container Registry and 1-Click Apps"
author: "Sergio"
categories: [ kubernetes ]
description: "Let’s be honest: Kubernetes is complicated. And since we’re obsessed with simplifying the developer experience, we continually ask ourselves: How do we make using Kubernetes easier?

It’s with this in mind that we’re pleased to announce DigitalOcean Container Registryhttps://www.digitalocean.com/products/containerregistry/ and our fastgrowing collection of more than fifteen Kubernetes 1Click Appshttps://marketplace.digitalocean.com/category/kubernetes. Together, these enhancements make developme"
image: "https://sergio.afanou.com/assets/images/image-midres-39.jpg"
---


Let’s be honest: Kubernetes is complicated. And since we’re obsessed with simplifying the developer experience, we continually ask ourselves: How do we make using Kubernetes easier?

It’s with this in mind that we’re pleased to announce [DigitalOcean Container Registry](https://www.digitalocean.com/products/container-registry/) and our fast-growing collection of more than fifteen [Kubernetes 1-Click Apps](https://marketplace.digitalocean.com/category/kubernetes). Together, these enhancements make development and operations with Kubernetes much smoother sailing.

Easily store and manage private container images
------------------------------------------------

After building your apps into Docker containers, you’ll often want to store container images in a centralized location called a container registry. From there, you can pull images into a Kubernetes cluster or VM, whether it’s a development, testing, staging, or production environment.

While you can post your container images on the open Internet freely using a service like Docker Hub, clearly you won’t want to do that with confidential software. And while you can also build and operate a registry on your own private network, it’s probably not the best use of your engineers’ time. That’s why we’re excited to introduce a new managed service: [DigitalOcean Container Registry](https://www.digitalocean.com/products/container-registry/), now available in Early Access. DigitalOcean Container Registry is all of these:

* **Simple** - Build your container images on any machine, and push them to DigitalOcean Container Registry with the [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/). DigitalOcean Kubernetes seamlessly integrates with this service to facilitate continuous deployment using container images stored there.
* **Private** - Protect sensitive software by storing container images in private repositories. With [DigitalOcean Teams](https://www.digitalocean.com/products/teams/), you can easily give access to coworkers – and no one else.
* **Secure** - Transfer container images over high speed HTTPS connections to servers across four continents. Container images are stored safely and encrypted at rest.
* **Fast** - Store your container images near your Kubernetes clusters and Droplets and enjoy low latency and free transfer over the private network.
![](https://images.prismic.io/www-static/68b2e34b-cee8-428f-bd65-e1089df8c047_digitalocean-container-registry-ui.png?auto=compress,format)

*Container Registry is integrated into the Images section of cloud.digitalocean.com*

We’ll be inviting folks to try [DigitalOcean Container Registry](https://www.digitalocean.com/products/container-registry/) in the weeks ahead. And the service is free to use during the Early Access program.

Deploy software to clusters with 1-Click Apps
---------------------------------------------

DigitalOcean introduced 1-Click Apps back in 2012, making it super simple to deploy popular software stacks to your VMs. We’ve now brought the same 1-Click experience to Kubernetes, so you can easily deploy complex software packages to your clusters.

Within [DigitalOcean Marketplace](https://marketplace.digitalocean.com/), you’ll find a dedicated [Kubernetes section](https://marketplace.digitalocean.com/category/kubernetes) with more than fifteen 1-Click Apps specifically packaged and tested for deployment to DigitalOcean Kubernetes.

![](https://images.prismic.io/www-static/4a9bc9d3-593f-47bb-a6fd-857a36e8a3c4_digitalocean-kubernetes-1-click-apps.png?auto=compress,format)

Broadly speaking, you can think about Kubernetes apps as addressing various use cases:

* **Critical cluster operations** - [Monitoring Stack](https://marketplace.digitalocean.com/apps/kubernetes-monitoring-stack), [Grafana Loki](https://marketplace.digitalocean.com/apps/grafana-loki), [Linkerd](https://marketplace.digitalocean.com/apps/linkerd), [Netdata](https://marketplace.digitalocean.com/apps/netdata), [Red Sky Ops](https://marketplace.digitalocean.com/apps/red-sky-ops), [NGINX Ingress Controller](https://marketplace.digitalocean.com/apps/nginx-ingress-controller), and [Metrics](https://marketplace.digitalocean.com/apps/kubernetes-metrics-server) help with fundamentals like monitoring, performance, logging, or security.
* **Developer-friendly tools** - [OpenFaaS](https://marketplace.digitalocean.com/apps/openfaas-kubernetes), [KubeMQ](https://marketplace.digitalocean.com/apps/kubemq), [Okteto](https://marketplace.digitalocean.com/apps/okteto-1), [OpenEBS](https://marketplace.digitalocean.com/apps/openebs-1), [Argo CD](https://marketplace.digitalocean.com/apps/argo-cd), and [Moon](https://marketplace.digitalocean.com/apps/moon) create easily consumable APIs or services for your development teams
* **Consumer or enterprise apps** - [Wordpress](https://marketplace.digitalocean.com/apps/wordpress-kubernetes) , [Mattermost](https://marketplace.digitalocean.com/apps/mattermost-operator), [1Password SCIM Bridge](https://marketplace.digitalocean.com/apps/1password-scim-bridge) run popular services you can use for hobbies or work.
We continue to add new applications to the DigitalOcean Marketplace almost every week. If you’d like to submit your Kubernetes application for consideration, submit a pull request on our [Marketplace Github repo](https://github.com/digitalocean/marketplace-kubernetes/blob/master/CONTRIBUTING.md).

New DigitalOcean Kubernetes features and customers
--------------------------------------------------

Like many of you do for your products, we continuously deliver new features and enhancements for DigitalOcean Kubernetes. We recently introduced support for [Kubernetes 1.16](https://kubernetes.io/blog/2019/09/18/kubernetes-1-16-release-announcement/), and in case you missed it, last month we announced [cluster autoscaling](https://www.digitalocean.com/docs/kubernetes/how-to/configure-autoscaling/), [minor version upgrades](https://www.digitalocean.com/docs/kubernetes/how-to/upgrade-cluster/), and [tokenized authentication](https://www.digitalocean.com/docs/kubernetes/how-to/connect-to-cluster/).

![](https://images.prismic.io/www-static/703a0786-3d3b-4499-af91-0a34d09e36be_digitalocean-kubernetes-autoscale-1.png?auto=compress,format)

With every new feature, we eat our own dog food to make sure that things work. [Read how DigitalOcean Marketplace runs at global scale using DigitalOcean Kubernetes](https://blog.digitalocean.com/how-we-launched-our-marketplace-using-digitalocean-kubernetes-part-1/).

And, with thousands of DigitalOcean Kubernetes clusters running successfully every day, you can have confidence in our platform. Here’s what Paritosh Gupta, CTO of [Orai](http://www.orai.com/) – an AI speech coach and [Hatch](https://www.digitalocean.com/hatch/) startup – had to say about his experience:

*“Setting up DigitalOcean Kubernetes was super fast. Our team can easily add new features and update the machine learning models that power the Orai app. We can ensure our technology stack processes everything in realtime, without worrying about the time or cost to rebuild our infrastructure.” -  
**Paritosh Gupta, CTO***


Get started with DigitalOcean Kubernetes and Container Registry today
---------------------------------------------------------------------

Hopefully the new features and enhancements make it easier for you to set up and operate your Kubernetes clusters in production. It takes just a few minutes to try DigitalOcean Kubernetes. Simply [sign into your account and spin up](https://cloud.digitalocean.com/kubernetes/clusters/new). You can also check out our new [Kubernetes curriculum on the DigitalOcean Community](https://www.digitalocean.com/community/curriculums/kubernetes-for-full-stack-developers) to help you learn what has become the de facto container orchestration platform. We’re also hosting a [series of webinars to help you advance your Kubernetes skills](https://go.digitalocean.com/WEBAdvanced-K8s-With-DO-General_MainLandingPagev1).

If you’d like to discuss how to use DigitalOcean Kubernetes to run your applications, we invite you to [contact our sales team](https://www.digitalocean.com/company/contact/sales/). Or, if you happen to be at [KubeCon in San Diego](https://events19.linuxfoundation.org/events/kubecon-cloudnativecon-north-america-2019/) this week, come by our booth (G18) or [one of our tech talks](https://kccncna19.sched.com/?searchstring=digitalocean) to speak with me, our engineers, and developer advocates.

Happy Kubecon!  
Phil Dougherty, Senior Product Manager

