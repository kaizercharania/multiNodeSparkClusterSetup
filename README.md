# Multi Node Spark Cluster Setup
### Steps for setting up Spark Cluster on Amazon Web Services (AWS) Ec2 Instances.
#### This article will guide you through setting up Spark Cluster on Amazon Ec2 Instances.
**Prereq: Amazon Web Services account**
Follow these step to make a Spark Cluster up and Running:

1. Create EC2 Instances. It depends on you, if you want 4 slave nodes and 1 master node, then create 5 EC2 instance.
**Note: As Spark needs minimum 3GB RAM per node to perform tasks, please use T2.Medium instance type or above.** This does not come under Free Tier. I have used 3 T2.Medium instance type (1 Master and 2 slaves) with Ubuntu 14.04
2. SSH to one of your Ec2 Instance. This will be your master node.
3. Run the following command and add those lines to the hosts file:
          ```
          sudo nano /etc/hosts
          ```
   1. add these lines to the file.
        ```
        MASTER-IP master
        SLAVE01-IP slave01
        SLAVE02-IP slave02
        ```
        Where MASTER-IP, SLAVE01-IP, and SLAVE02-IP are private IPs of your EC2 instances
4. Install Java 8
   ```
          sudo add-apt-repository ppa:webupd8team/java
          sudo apt-get update
          sudo apt-get install oracle-java8-installer
   ```
5. Install Scala
   ```    
          sudo apt-get install scala
   ```
6. Install the ssh client so that you can ssh to slaves from master node
   ``` 
          sudo apt-get install openssh-server openssh-client
   ```
7. Generate the key pairs using
   ```
          ssh-keygen -t rsa -P ""
   ```
   1. This will create two keys: private (id_rsa) and public( id_rsa.pub)
   2. Copy the content of id_rsa.pub to the following file ~/.ssh/authorised_keys of all the instances (master node as well).
8.  Try sshing to slave01 and slave02 from master node.

### So, now the connection between your cluster is setup. Next, we will install spark on master node, change some config and then copy that config file to all the slaves
9.  Download Spark ```sudo wget http://apache.claz.org/spark/spark-2.4.0/spark-2.4.0-bin-hadoop2.7.tgz ```
10. Untar the file ```tar xzf spark-2.4.0-bin-hadoop2.7.tgz ```
11. Now edit /.bashrc file and add the following lines to the bottom of bashrc file
    ```
          export JAVA_HOME=/usr/lib/jvm/java-8-oracle/
          export SPARK_HOME=/home/ubuntu/spark-2.4.0-bin-hadoop2.7
          export PATH=$PATH:$SPARK_HOME/bin
    ```
12. Now, we have to create a spark-env.sh file that will be a configuration to our spark cluster.
    1. cd conf folder in and perform the command ```cp spark-env.sh.template spark-env.sh```
    2. Add these lines to the spark-env.sh file 
      ```
      export JAVA_HOME=/usr/lib/jvm/java-8-oracle/
      export SPARK_WORKER_CORES=8
      ```
13. Create another file named "slaves" to this folder and add 
    ```
          slaves01
          slaves02
    ```
14. Create a TarBall of the configured setup
    ```
          tar czf spark.tar.gz spark-2.4.0-bin-hadoop2.7
    ```
#### Installation on Master node is finished. Now, we need to install Spark and other modules to Slaves Instances.


## **Install Java8, Scala and spark tar file on all the slaves**
15. To  copy Tar file to the slaves node, use ```scp spark.tar.gz slave01:~```
16. Untar those files on slaves instance
17. Perform from step3 to step5

##### Goto to the Master node and start the server using ```sbin/start-all.sh```
--> You can view the spark UI on **master-public-ip:8080** and application UI on port **4040**

###### To Start the Spark Shell, enter ``` ./bin/spark-shell --master <spark-ip> --executor-memory 512m```
