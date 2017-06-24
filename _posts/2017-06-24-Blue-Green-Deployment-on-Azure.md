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
The diagram below shows the overall architecture. You deploy your resources into your resource groups. In this diagram we have one resource group for the green environment (which has the old code) and one resource group for the blue environment (which has the new code). Your code is hosted on virtual machines which are part of a VM Scale Set (VMSS). The VMSS can be configured to auto scale based on the rules. The VMSS uses a custom OS image to spin up new virtual machines. 

Everything inside the resource group has a private IP address. In each resource group there is an Internal Load Balancer with also a private frontend IP address which load balances the incoming traffic across the backend VMs. 

The Virtual Network is defined inside the frontend resource group and all the resoruces in different resource groups are all members of the same virtual network (in different subnets though). The Application Gateway in the frontend resource group receives all incoming traffic and delivers it to the backend Load Balancers.  

![Blue-Green](/images/Blue-Green-2.png)

### Now take the steps below to deploy
Here are the steps we are going to take:
1. Deploy the frontend resource group
2. Deploy the green resource group (considering it has your old code)
3. Deploy the blue resource group (considering it has your new code)
4. Redirect the traffic from the green resource group to the blue resource group (or in other words SWAP)

>To deploy the green or blue resource group you need to host/install your new code on a VM, generlize the VM, shut it down, and then provide a reference to the image in the code which comes in this post. 

