---
title: "Docker"
date: 2021-12-12T13:58:19Z 
draft: false 
categories: ["docker", "containers"]
tags: ["Docker"]
searchHidden: false
---

## Docker
> ### Docker is an open source platform for building, deploying, and managing containerized applications. Learn about containers, how they compare to VMs, and why Docker is so widely adopted and used.
***

## What is Docker?

Docker is an open source containerization platform. It enables developers to package applications into
containers—standardized executable components combining application source code with the operating system (OS) libraries
and dependencies required to run that code in any environment. Containers simplify delivery of distributed applications,
and have become increasingly popular as organizations shift to cloud-native development and hybrid multicloud
environments. Developers can create containers without Docker, but the platform makes it easier, simpler, and safer to
build, deploy and manage containers. Docker is essentially a toolkit that enables developers to build, deploy, run,
update, and stop containers using simple commands and work-saving automation through a single API. Docker also refers to
Docker, Inc. (link resides outside IBM), the company that sells the commercial version of Docker, and to the Docker open
source project (link resides outside IBM), to which Docker, Inc. and many other organizations and individuals
contribute.
***

## How containers work, and why they're so popular

Containers are made possible by process isolation and virtualization capabilities built into the Linux kernel. These
capabilities - such as control groups (Cgroups) for allocating resources among processes, and namespaces for restricting
a processes access or visibility into other resources or areas of the system - enable multiple application components to
share the resources of a single instance of the host operating system in much the same way that a hypervisor enables
multiple virtual machines (VMs) to share the CPU, memory and other resources of a single hardware server. As a result,
container technology offers all the functionality and benefits of VMs - including application isolation, cost-effective
scalability, and disposability - plus important additional advantages:

1. **Lighter weight:** Unlike VMs, containers don’t carry the payload of an entire OS instance and hypervisor; they
   include only the OS processes and dependencies necessary to execute the code. Container sizes are measured in
   megabytes (vs. gigabytes for some VMs), make better use of hardware capacity, and have faster startup times.
2. **Greater resource efficiency:** With containers, you can run several times as many copies of an application on the
   same hardware as you can using VMs. This can reduce your cloud spending.
3. **Improved developer productivity:** Compared to VMs, containers are faster and easier to deploy, provision and
   restart. This makes them ideal for use in continuous integration and continuous delivery (CI/CD) pipelines and a
   better fit for development teams adopting Agile and DevOps practices.

Improved developer productivity: Compared to VMs, containers are faster and easier to deploy, provision and restart.
This makes them ideal for use in continuous integration and continuous delivery (CI/CD) pipelines and a better fit for
development teams adopting Agile and DevOps practices.
***

## Why use Docker?

Docker is so popular today that “Docker” and “containers” are used interchangeably. But the first container-related
technologies were available for years — even decades (link resides outside IBM) — before Docker was released to the
public in 2013. Most notably, in 2008, LinuXContainers (LXC) was implemented in the Linux kernel, fully enabling
virtualization for a single instance of Linux. While LXC is still used today, newer technologies using the Linux kernel
are available. Ubuntu, a modern, open-source Linux operating system, also provides this capability. Docker enhanced the
native Linux containerization capabilities with technologies that enable:

1. **Improved—and seamless—portability:** While LXC containers often reference machine-specific configurations, Docker
   containers run without modification across any desktop, data center and cloud environment.
2. **Even lighter weight and more granular updates:** With LXC, multiple processes can be combined within a single
   container. With Docker containers, only one process can run in each container. This makes it possible to build an
   application that can continue running while one of its parts is taken down for an update or repair.
3. **Automated container creation:** Docker can automatically build a container based on application source code.
4. **Container versioning:** Docker can track versions of a container image, roll back to previous versions, and trace
   who built a version and how. It can even upload only the deltas between an existing version and a new one.
5. **Container reuse:** Existing containers can be used as base images—essentially like templates for building new
   containers.
6. **Shared container libraries:** Developers can access an open-source registry containing thousands of
   user-contributed containers.

Today Docker containerization also works with Microsoft Windows server. And most cloud providers offer specific services
to help developers build, ship and run applications containerized with Docker. For these reasons, Docker adoption
quickly exploded and continues to surge. At this writing, Docker Inc. reports 11 million developers and 13 billion
container image downloads every month (link resides outside IBM).
***

## Docker tools and terms

Some of the tools and terminology you’ll encounter when using Docker include:

### DockerFile

Every Docker container starts with a simple text file containing instructions for how to build the Docker container
image. DockerFile automates the process of Docker image creation. It’s essentially a list of command-line interface (
CLI) instructions that Docker Engine will run in order to assemble the image.

### Docker images

Docker images contain executable application source code as well as all the tools, libraries, and dependencies that the
application code needs to run as a container. When you run the Docker image, it becomes one instance (or multiple
instances) of the container. It’s possible to build a Docker image from scratch, but most developers pull them down from
common repositories. Multiple Docker images can be created from a single base image, and they’ll share the commonalities
of their stack. Docker images are made up of layers, and each layer corresponds to a version of the image. Whenever a
developer makes changes to the image, a new top layer is created, and this top layer replaces the previous top layer as
the current version of the image. Previous layers are saved for rollbacks or to be re-used in other projects. Each time
a container is created from a Docker image, yet another new layer called the container layer is created. Changes made to
the container—such as the addition or deletion of files—are saved to the container layer only and exist only while the
container is running. This iterative image-creation process enables increased overall efficiency since multiple live
container instances can run from just a single base image, and when they do so, they leverage a common stack.

### Docker containers

Docker containers are the live, running instances of Docker images. While Docker images are read-only files, containers
are live, ephemeral, executable content. Users can interact with them, and administrators can adjust their settings and
conditions using docker commands.

### Docker Hub

Docker Hub (link resides outside IBM) is the public repository of Docker images that calls itself the “world’s largest
library and community for container images.” It holds over 100,000 container images sourced from commercial software
vendors, open-source projects, and individual developers. It includes images that have been produced by Docker, Inc.,
certified images belonging to the Docker Trusted Registry, and many thousands of other images. All Docker Hub users can
share their images at will. They can also download predefined base images from the Docker filesystem to use as a
starting point for any containerization project.

### Docker daemon

Docker daemon is a service running on your operating system, such as Microsoft Windows or Apple MacOS or iOS. This
service creates and manages your Docker images for you using the commands from the client, acting as the control center
of your Docker implementation.

### Docker registry

A Docker registry is a scalable open-source storage and distribution system for docker images. The registry enables you
to track image versions in repositories, using tagging for identification. This is accomplished using git, a version
control tool.
***

## Docker deployment and orchestration

If you’re running only a few containers, it’s fairly simple to manage your application within Docker Engine, the
industry de facto runtime. But if your deployment comprises thousands of containers and hundreds of services, it’s
nearly impossible to manage that workflow without the help of these purpose-built tools.

### Docker Compose

If you’re building an application out of processes in multiple containers that all reside on the same host, you can use
Docker Compose to manage the application’s architecture. Docker Compose creates a YAML file that specifies which
services are included in the application and can deploy and run containers with a single command. Using Docker Compose,
you can also define persistent volumes for storage, specify base nodes, and document and configure service dependencies.

### Kubernetes

To monitor and manage container lifecycles in more complex environments, you’ll need to turn to a container
orchestration tool. While Docker includes its own orchestration tool (called Docker Swarm), most developers choose
Kubernetes instead. Kubernetes is an open-source container orchestration platform descended from a project developed for
internal use at Google. Kubernetes schedules and automates tasks integral to the management of container-based
architectures, including container deployment, updates, service discovery, storage provisioning, load balancing, health
monitoring, and more. In addition, the open source ecosystem of tools for Kubernetes—including Istio and Knative—enables
organizations to deploy a high-productivity Platform-as-a-Service (PaaS) for containerized applications and a faster
on-ramp to serverless computing.

For a deeper dive on Kubernetes, see out video “Kubernetes Explained”:

{{< youtube VnvRFRk_51k >}}
***
