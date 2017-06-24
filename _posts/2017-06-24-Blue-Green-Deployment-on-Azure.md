---
layout: post
title: Blue-Green Deployment on Azure with Zero Downtime
subtitle:
category: howto
tags: [cloud, devops]
author: esmaeil_sarabadani
author_email: esmaeil.sarabadani@haufe-lexware.com
header-img: "images/bg-post.jpg"
---

### What is Blue-Green deployment?
In simple words, when you are updating your code/resources you need to make sure the shift from your old code to the new one does not impact your users. So it needs to be seamless with absolutely no downtime. Otherwise you are going to lose your customers and I'm pretty sure that's not what you want. Is it? 

So what most developers do is create two parallel environments and they call them (you guessed it right) Blue and Green. Green is the existing production environment (with the old code) and Blue is the new environment (with the new code) which eventually becomes the new Green. When we say Blue-Green deployment we are talking about the shift from Blue to become the new Green, and if you do this whole process right this shift should bring no downtime to your environment. 

### Blue-Green Deployment on Azure
Depending on your workload there are different ways of doing such deployment on Azure. One of the more common ways of doing such deployment on Azure was to use "Cloud Services" (which is a PaaS service). Beside blue-green deployment Cloud Service would also bring you the possibility to autoscale your virtual machines at ease. So what was the problem with it? Cloud Service is a classic service which also uses the old Azure Service Manager API. Cloud Service is still available on Azure and can be used but Microsoft strongly recommends against using it because soon or late they are going to get rid of Azure Service Manager API. 

Instead Microsoft suggests the use of Azure Resource Manager (ARM) API but what is an alternative to Cloud Service on ARM? Depending on your code and resources you could use App Service as well as Service Fabric on Azure. but today in this blog post we do blue-green deployment using VM Scale Sets. This way you can use Virtual Machines hosting your code and have the possibility to auto scale based on specific rules. These rules are based on the usage of CPU, memory, and etc. and trigger a scale-up or down when the usage is too high or low. (In this blog post we are not focusing much on the auto-scaling rules because that is not the main question for many but we focus mainly on the whole architecture and code).

### Blue-Green Architecture on Azure Resource Manager

![Architecture]({{ site.url }}/images/Blue-Green-2.png){:style="margin:auto; width:50%"}
