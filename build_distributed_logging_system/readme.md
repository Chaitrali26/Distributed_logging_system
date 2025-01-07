# Build a Distributed Logging System

We will dive into distributed logging by setting up a centralized logging system. 
This hands-on challenge will help understand the essentials of log management, message queues, and monitoring, all critical to modern DevOps practices.


## Requirements:
### Install and Configure Your Message Queue:
### Option A: RabbitMQ

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

### Option B: Redis

### Install Redis:
```
sudo apt update
sudo apt install redis-server -y
```

### Enable Authentication:
Edit /etc/redis/redis.conf and enable password authentication:
```
requirepass your_password
```

### Restart Redis:
```
sudo systemctl restart redis-server
```
 
### Configure Network Access:
Allow external access by modifying the bind setting in /etc/redis/redis.conf:
```
bind 0.0.0.0
```

### Restart Redis:
```
sudo systemctl restart redis-server
```


## Validation Steps
### For RabbitMQ:
```
Log in to the RabbitMQ management UI and verify the logs_queue exists.
Use the CLI to check the queue:
sudo rabbitmqctl list_queues
```

### For Redis:
Use the Redis CLI to verify the connection and authentication:
```
redis-cli -a your_password ping
Check the queue length:
redis-cli -a your_password LLEN logs_queue
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
