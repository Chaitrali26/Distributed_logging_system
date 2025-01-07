# Build a Distributed Logging System

We will dive into distributed logging by setting up a centralized logging system. 
This hands-on challenge will help understand the essentials of log management, message queues, and monitoring, all critical to modern DevOps practices.


## Requirements:
### Install and Configure Your Message Queue with RabbitMQ:

### Install RabbitMQ:
```
sudo apt update sudo apt install rabbitmq-server -y
```
 
### Enable Management Plugin:
sudo rabbitmq-plugins enable rabbitmq_management
sudo systemctl restart rabbitmq-server
 
Access the RabbitMQ UI at http://<server-ip>:15672. Default credentials: guest/guest.

### Set Up a User and Queue:
```
sudo rabbitmqctl add_user your_username your_password sudo rabbitmqctl set_permissions -p / your_username ".*" ".*" ".*" sudo rabbitmqctl add_queue logs_queue
```
 
### Configure Network Access:
Edit /etc/rabbitmq/rabbitmq.conf to allow external access:
```
listeners.tcp.default = 5672
```

### Restart RabbitMQ:
```
sudo systemctl restart rabbitmq-server
```

## Validation Steps
### For RabbitMQ:
```
Log in to the RabbitMQ management UI and verify the logs_queue exists.
Use the CLI to check the queue:
sudo rabbitmqctl list_queues
```

## Why This Matters
Message queues form the backbone of scalable and decoupled systems. 
By setting up RabbitMQ or Redis, you’re preparing a solid foundation for log ingestion and aggregation, which we’ll implement in next.

Now, we will implement a log producer and consumer system using RabbitMQ. 
By the end, we have a distributed logging system that can produce log messages and aggregate them into a file for analysis.

## Requirements:
### Install Required Python Libraries:
``` pip install pika ```

### Create the Producer Script (log_producer.py):
- This script simulates log generation and sends log messages to the RabbitMQ queue.

### Test the Producer:

- Run the producer script to send a log message:

``` python log_producer.py "Log Entry 1: This is a sample log."```

- Create the Consumer Script (log_aggregator.py):
- This script will read log messages from the logs_queue and write them to a file. 

### Test the Consumer:
- Run the consumer script:
``` python log_aggregator.py```
- Send multiple logs using the producer, and confirm the messages are saved in aggregated_logs.txt.

### Verify the Logs:
- Open the aggregated_logs.txt file to ensure all logs are captured.

## Why This Matters
This showcases how RabbitMQ can be used to decouple log production and consumption, creating a scalable and fault-tolerant system. 
Such systems are widely used in real-world applications for log aggregation, task queues, and event-driven architectures.

## Additional Tasks:
- Visit the RabbitMQ official [Work Queues tutorial ](https://www.rabbitmq.com/tutorials/tutorial-one-python)
- Follow the instructions to set up producer and worker queues.
- Test the system by running multiple worker instances and observing how tasks are distributed.
- Learn Key Concepts - Message acknowledgement to ensure reliability, Fair dispatch to avoid overloading a single worker, Durable queues and persistent messages to handle failures.

# Monitor RabbitMQ with Prometheus and Grafana

we’ll dive into monitoring RabbitMQ using Prometheus and Grafana. By the end of this challenge, you'll have a fully functional RabbitMQ monitoring setup, providing insights into queue performance, node health, and more.

### Built-in Prometheus Support
RabbitMQ ships with built-in Prometheus and Grafana support.
- Prometheus Metrics:
  The rabbitmq_prometheus plugin exposes all RabbitMQ metrics on a dedicated TCP port, in Prometheus text format. These metrics provide deep insights into the state of RabbitMQ nodes and the runtime, enabling informed analysis of RabbitMQ behaviour, applications, and infrastructure.

### Grafana Support
RabbitMQ comes with prebuilt Grafana dashboards to visualize metrics effectively.

Dashboards available:
- Overview Dashboard: General RabbitMQ performance and health.
- Runtime Memory Allocators Dashboard: Insights into memory allocation.
- Inter-Node Communication Dashboard: Erlang distribution metrics for cluster communication.
- Raft Metrics Dashboard: Quorum queue and leader election data.
- Others: Dashboards for specific subsystems and metrics.

These dashboards are opinionated, customizable, and follow best practices to spot health issues quickly and provide detailed system insights.


## Prerequisites
Set Up RabbitMQ with Docker Compose and/or Multinode Cluster

### Docker Compose Setup

Step 1: Clone the Repository with Manifests
To begin, clone the RabbitMQ repository that contains the necessary manifests for RabbitMQ, Prometheus, and Grafana:
```
git clone https://github.com/rabbitmq/rabbitmq-server.git
cd rabbitmq-server/deps/rabbitmq_prometheus/docker
```

Step 2: Run Docker Compose to Set Up RabbitMQ, Prometheus, and Grafana
Use Docker Compose to start the RabbitMQ cluster and Prometheus instance, along with a basic workload to generate meaningful metrics:
```
docker-compose -f docker-compose-metrics.yml up -d
docker-compose -f docker-compose-overview.yml up -d
```

This will start a RabbitMQ cluster, Prometheus, and Grafana with predefined configurations, collecting metrics from RabbitMQ.

Step 3: Access Grafana Dashboard
After the setup, navigate to Grafana’s dashboard page at http://localhost:3000/dashboards.

You will be prompted for login credentials:
```
Username: admin
Password: admin
```
Once logged in, Grafana will recommend changing your password, but you can skip this step for the demonstration.

Navigate to the RabbitMQ Overview dashboard. 
This dashboard visualizes the metrics collected by Prometheus from the RabbitMQ cluster.


### 3 node cluster Setup on AWS EC2
1. Install RabbitMQ on all nodes
Configure RabbitMQ as a cluster across the three EC2 nodes.

2. RabbitMQ Configuration
Give the RabbitMQ cluster a descriptive name so that it can be distinguished from other clusters

3. Enable the Prometheus plugin on all nodes
Enable the RabbitMQ Prometheus plugin to expose metrics.
Run rabbitmq-plugins enable rabbitmq_prometheus on all nodes.
Follow the RabbitMQ Prometheus Plugin Documentation for installation and configuration.

4. Prometheus Configuration
Install Prometheus on a separate EC2 instance or any other machine in the network.
Configure Prometheus to scrape RabbitMQ metrics from each node’s exposed Prometheus endpoint (http://node-ip:15692/metrics).

5. Grafana Configuration
Install Grafana on a separate instance or machine.
Add Prometheus as a data source in Grafana.
Import the RabbitMQ Overview dashboard from Grafana's dashboard repository.

Once you've completed these steps, you’ll have a fully functional RabbitMQ cluster with Prometheus scraping metrics and Grafana providing visualizations. 
For more details, refer to the individual documentation links provided for each component.


## Why This Matters:
By setting up RabbitMQ with Prometheus and Grafana, you will gain deep insights into RabbitMQ’s performance and troubleshoot issues faster. 
These tools provide essential metrics, allowing you to monitor your cluster’s health and performance at scale.

