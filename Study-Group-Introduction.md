# Study Group Introduction

## Docker Enterprise Edition 

Docker Enterprise Edition (DockerEE) provides a support mechanism for issues with an underlying engine as well as deployment challenges using it. When using DockerEE on a certified platform, organizations are assured through Docker’s certification that their applications will work as expected and that they will have support if they do not.
   
### Supported  Platforms

- CentOS
- Debian 
- Fedora
- Ubuntu
- Oracle Linux
- Microsoft windows Server 2016
- RedHat Enterprise Linux
- SUSE Linux Enterprise Server

   
### Tiers

- Basic: Platform for certified infrastructure, containers, and plugins with support from Docker. 
- Standard: Adds advanced image and container management, LDAP, and RBAC.
- Advanced: Adds security scanning and vulnerability scanning. 


## Docker Swarm 

Docker swarm is a clustering and scheduling tool for the clusters of Docker containers (grouped together as services). Swarms allow portability, abstraction, flexibility, and consistency of complex application service deployments on a supported infrastructure. 

- Docker swarm managers are responsible for validating, logging the state of, and distributing instructions to Docker Swarm Workers. 
- Docker Service daemon is installed on every node in a swarm. 

### Basic Swarm Architecture

- Swarms can have a single manager; however, they can have 1 to any number of swarm managers:
  - The amount of managers you have determines the quorum:
    - You need to have at least 2 managers to have a quorum. 
    - A quorum is the consciences method of agreeing on the instructions and how they will be communicated to the workers.
  - Swarm workers will have Docker Daemon installed and will register using the Docker discovery service that runs on the Swarm manager:
    - Workers will receive workloads from the Swarm managers.
    - Swarm manager receives a request to access services:
      - Services are 1 to any number of containers (or replicas) that provide a particular application or service.
      - Access to that service can be accessed from any node.
      - Routing mesh is used to guarantee access.

