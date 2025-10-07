# VMWare - Scaling HBase & Hadoop & ZooKeeper (Clustering)

## Tasks
- Setup Docker Swarm Cluster that has 8 nodes on VMWare for test scaling.
- Setup images for HBase, Hadoop, Zookeeper for distribution deployment.
- Build Docker compose file to automate setup and deploy images on nodes in swarm cluster.
- Setup Kubernetes Cluster.
- Define architecture for real deployment to ensure scalable.
- Manually create cluster and setup Hadoop & HBase.

## Issues

### Issue 1
Hadoop and HBase required to start services in order. But Swarm cluster and Kubernetes cluster can't handle the service to start order.

### Solution 1
Most Hadoop and HBase cluster are use traditional cluster is manual cluster (manual create cluster and setup). I tested with manual cluster is work fine.

## Scalable Solution

Deploy all Hadoop services (namenode, datanode, nodemanager, secondarynamenode, resourcemanager), all HBase services (HMaster, HRegionserver) and Zookeeper in the single server. To scale Hadoop, we scale on datanode by setup datanode and nodemanager on new node and connect to namenode in our first server. For scale HBase, we scale on HRegionserver by setup HBase on new server and start only HRigionserver and connect to HMaster in our first server also. I have experimented on this method already, it works fine.

## Documentations
1. Scalable Hadoop and HBase: https://www.notion.so/Scalable-Hadoop-and-HBase-from-One-to-Many-25cb37f2e6d4808b9ad9e2460a6b8836?source=copy_link
2. Setup Kubernetes: https://www.notion.so/Setup-Kubernets-Cluster-25bb37f2e6d4804fada7e8af2b22b28a?source=copy_link 
3. How to deploy Hadoop and HBase on Swarm Cluster: https://www.notion.so/Scalable-Hadoop-and-HBase-on-Swarm-Cluster-25cb37f2e6d480fa88e2f0c3fb164ef6?source=copy_link
