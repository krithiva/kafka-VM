Building Apache Kafka cluster using docker-compose and VirtualBox
 
Apache Kafka is a an open-source stream-processing software platform, designed for high-throughput, low-latency and real-time data broadcasting. It’s provided by an easy-scalable and high-availability environment. Let’s see how to configure your own docker-compose recipe with fully functional Apache Kafka clustered environment just in few minutes.
Overview
•	Preparing host machine for clustered environment using VirtualBox, docker and docker-compose.
•	Creating docker-compose recipe file – step by step guide.
•	The final version of Apache Kafka cluster docker-compose.yml file.
•	Testing Apache Kafka cluster using kafkacat tool.
 

 1.Installing docker and docker-compose tools
The next step is to install some necessary tools like docker and docker-compose which allow you to manage many different containers with complex dependencies in a very understandable and compact form.

sudo apt-get update
sudo apt-get install docker.io docker-compose

 
2. Creating Apache Kafka cluster using docker-compose
It is time to add three more containers to docker-compose.yml file which belongs to the Kafka cluster. Newly created servers are pointed to already prepared ZooKeeper cluster as it is shown on the image below.
 
Each kafka node exposes #9092 client port.


2.1 Apache Kafka cluster docker-compose.yml file
Attachment file has the details

	
 
3. Testing Apache Kafka cluster using kafkacat tool
In this step I will show you how to use kafkacat tool to test previously created Kafka cluster. We will send a message to the first node of the cluster and we will see if we will receive the same message from the third node of the cluster as it is shown on image below.
 
 
Please remember to add kafka-1, kafka-2 and kafka-3 hosts to the client /etc/hosts file.
/et/hosts file:
1
2
3
4
5
6
7	127.0.0.1	localhost
 
 
192.168.1.231 kafka-1 kafka-2 kafka-3
 
# The following lines are desirable for IPv6 capable hosts
::1     ip6-localhost ip6-loopback
 

Now install kafkacat using the following command:
Ubuntu: kafkacat installation

1	sudo apt-get install kafkacat
	


Run the following command to list all available brokers in the cluster:
1	kafkacat -L -b kafka-1:19092
 As you can see all of three nodes are accessible:
The result of command: kafkacat -L -b kafka-1:19092
1
2
3
4
5
6
7
8
9
10
11	better-coding@bc-vbox:~$ kafkacat -L -b kafka-1:19092
Metadata for all topics (from broker 1: kafka-1:19092/1):
 3 brokers:
  broker 2 at kafka-2:29092
  broker 1 at kafka-1:19092
  broker 3 at kafka-3:39092
 2 topics:
  topic "__confluent.support.metrics" with 1 partitions:
    partition 0, leader 2, replicas: 2,3,1, isrs: 2,3,1
  topic "helloworld.t" with 1 partitions:
    partition 0, leader 1, replicas: 1, isrs: 1
 Open two instances of terminal and run:
Terminal #2: kafkacat in Producer mode
1	kafkacat -P -b kafka-1:19092 -t helloworld_topic
 Terminal #2: kafkacat in Consumer mode
Shell
1	kafkacat -C -b kafka-3:39092 -t helloworld_topic
 Then write some message to the first terminal, and you should see the same message in the second terminal.
Terminal #2: kafka Consumer output


	>kafkacat -C -b kafka-3:39092 -t helloworld_topic
% Reached end of topic helloworld_topic [0] at offset 0
 
test_message
% Reached end of topic helloworld_topic [0] at offset 1

