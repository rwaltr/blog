---
title: Centos Stream and Kubernetes 1.20.0v
date: 2020-12-20
author: Ryan Walter
tags: ["kubernetes", "centos","steam","containers","linux"]
---

# Centos Stream Really isnt that bad

I recently decided to deploy CentOS Stream due to the Recent announcment from both Redhat on their new focus and Kubernetes on their deprication of the Dockershim.

I decided to use ContainerD with Kubernetes 1.20.0V. CentOS Stream was installed with minimal install.

Currently testing a deployment using Calico as the network manager and the nfs-client-provisioner (Now known as the nfs-subdir-client-provisoner)

Eventally I will be using the following componets

- Calico Network
- BGP peering
- Traefik ingress controller
- External-DNS
- Cert-Manager
- Vault Secret Manager
- Node-Discovery
- HA Control node
- Kubevirt
