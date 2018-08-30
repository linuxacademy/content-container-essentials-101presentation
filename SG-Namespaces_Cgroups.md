# Docker Namespaces and Cgroups

Namespaces provide "Isolation" making it so that other pieces of the system remain unaffected by whatever is within that namespace.

Docker uses namespaces of various kinds to provide the isolation that containers need to remain portable and refrain from affecting the remainder of the host system. 

## Namespace Types
- Process ID — Allows the encapsulation of everything that is a container into a single process so that activity inside the container cannot affect the host system. 
- Mount — Can provide isolated mount points in a container. For example, volume mountings in a container.
- IPC — Allows containers and services to communicate with each other but not outside of the namespace.
- User — In Docker 1.12 and above there is the experimental usage of user namespaces. However, there are still issues, and it is essential to remember it is experimental and can break other isolation items if it has not been integrated correctly.
- Network — Deals with routing that occurs in a container.

## Control Groups
Control Groups provide resource limitation and reporting capability within the container space. They allow granular control over what host resources are allocated to the container(s) and when they are allocated. 

Common Control groups:

- CPU
- Memory
- Network Bandwith
- Disk
- Priority 