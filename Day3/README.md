# Day 3

## Why Red Hat Openshift supports only Podman and not Docker
<pre>
- Upto Red Hat Openshift v3.11, docker was the default container engine supported by Kubernetes and Openshift
- Docker had a security flaw, which gives administrative access to non-administrators
- There was a company called CoreOS with 2 interesting products
  1. rkt - container runtime
  2. Core OS - an operating system that enforces all best practices of Container Orchestration Platform
     - comes in 2 flavours
       - Fedora Core OS - opensource
       - Red Hat Enterpise Core OS ( RHCOS ) - used by paid variant of Openshift
- Red Hat acquired the Core OS company
  - they killed rkt container runtime, instead they developed CRI-O container runtime as an opensource product
  - Podman is the container engine which internally uses CRI-O container runtime
  - starting from Red Hat Openshift v4.x onwards
    - docker support was removed
    - openshift v4.x supports only CRI-O Container Runtime and Podman Container Engine
    - Red Hat Core Enterprise Core OS - is used in all master nodes within openshift
    - Red Hat Core Enterprise Core OS - can also be installed optionally in openshift worker nodes apart from RHEL  
</pre>

## Container Orchestration Platform Overview
<pre>
- though containers and images can be managed manually using Container Engines/runtimes, that is not the way it is used in the industry
- container orchestration platforms helps us manage containerized application workloads
- features supported
  - any containerized applications can be deployed 
  - High Availability (HA)
  - light-weight
  - allows deploying applications built in different programming languages
  - scale up/down manually/automatically
  - rolling update
    - helps in upgrading your application from one version to other without any downtime
    - can also undo i.e rollback to immediate previous version to any older version of the same application
  - service discovery
  - inbuilt monitoring facility
  - inbuilt load-balancing
  - security 
- Examples
  1. Docker SWARM
  2. Kubernetes
  3. Openshift
</pre>
