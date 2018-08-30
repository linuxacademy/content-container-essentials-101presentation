# Sizing a Docker Enviorment 


## Vocabulary

* **Universal Control Plane (UCP)**- Docker enterprise edition system that enables you to access your docker swarm resources, such as you manager and worker nodes, from a Web console instead of the command line.

* **Docker Trusted Registry (DTR)**
* **Docker EE**- Docker Engine with support.

## Things To Consider:

* CPU, memory and Disk- Your containerized application will have running on any system; so be sure that your system has the resources necessary to be able to keep your enviroment running.

* Concurrency is often forgotten in planning. It's important to know what are the load requirement of the application at peak and in total? Having this information will enable you to determine the optimal placement and the amount of hardware resources that you will need to allocate.

  * Understand what when and how your environment is going to be used. For example, if you are going to be deploying UCP, you would need 16 GIG RAM for managers and DTR nodes. Each of your workers would need 4 VCPUs for your worker nodes. You would need to ensure not only that your hardware could meet these standards but also remember these are minimum standards. This you would need to ensure these standards could stand up to your heaviest traffic.  

* Remember that some configuration variables are configurable and others that are not. So the timeout for ETCD is set to half a second and is configurable; however, the timeout for the RAFT consciences is 3 seconds and is not configurable.  So though you can change the ETCD timeout to fit your environment, you have to be sure that communication between manager and manager in your environment can occur within three seconds.

* Plan for load balancing so that you can better manage your workload

* Use an external certificate authority when you are working with external variables.


## Special notes:
 Docker Eneterpirse edition includes:
* Docker Engine with support from docker
* DTR
* UCP