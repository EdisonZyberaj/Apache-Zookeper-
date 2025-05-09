# Apache ZooKeeper Distributed Systems

This project implements three different distributed systems using Apache ZooKeeper:
1. **Cluster Node Monitoring**
2. **Distributed Locking**
3. **Distributed Queues**

Inside documentation folder, you can see detailed description and Outputs.

## Overview

The project demonstrates practical implementation of distributed coordination patterns using Apache ZooKeeper. Each system solves a specific distributed computing challenge as described below.

## Project Structure

```
DetyreKursi/
├── cluster-monitor/         # ZooKeeper cluster monitoring system
├── distributed-lock/        # Distributed locking implementation
├── distributed-queue/       # Distributed queue system
└── docker-compose.yaml      # Docker setup for ZooKeeper cluster
```

## Setup Instructions

### Prerequisites
- Java 21 or higher
- Maven
- Docker and Docker Compose (for running the ZooKeeper cluster)

### Running the ZooKeeper Cluster

The project includes a Docker configuration for a 5-node ZooKeeper cluster:

```bash
# Create the external Docker network first
docker network create zk-net

# Start the ZooKeeper cluster
docker-compose up -d
```

This will start 5 ZooKeeper nodes that communicate with each other to form a cluster. The nodes will be available at:
- localhost:2181
- localhost:2182
- localhost:2183
- localhost:2184
- localhost:2185

## 1. Cluster Node Monitoring

A monitoring system for tracking the status of each node in a ZooKeeper cluster.

### Features
- Real-time monitoring of ZooKeeper nodes
- Web dashboard for visualizing cluster status
- Tracks key metrics: node mode (leader/follower), connections, latency
- Automatic detection of node failures

### Running the Monitor

```bash
cd cluster-monitor
mvn clean package
java -jar target/cluster-monitor-1.0-SNAPSHOT-jar-with-dependencies.jar
```

Access the dashboard at http://localhost:8080

### Key Components

- **ZKClusterMonitor.java**: Main monitoring logic using ZooKeeper's four-letter commands
- **ZKDashboard.java**: Web-based dashboard for visualizing cluster status
- **Configuration**: Customizable via config.properties

## 2. Distributed Locking

Implementation of distributed mutual exclusion using ZooKeeper and Apache Curator.

### Features
- Thread-safe distributed locking
- Timeout support to prevent deadlocks
- Clean resource handling with AutoCloseable interface
- Demonstration of race conditions with and without locking

### Running the Lock Test

```bash
cd distributed-lock
mvn clean package
java -jar target/distributed-lock-1.0-SNAPSHOT-jar-with-dependencies.jar
```

### Key Components

- **ZKDistributedLock.java**: Lock implementation using Curator's InterProcessMutex
- **DistributedLockTest.java**: Demonstrates concurrent access with and without locking

## 3. Distributed Queues

A distributed queue system allowing multiple producers and consumers to work with a shared queue.

### Features
- Thread-safe queue operations
- Support for multiple concurrent producers and consumers
- Serializable message support
- Resilient to node failures and network partitions

### Running the Queue Test

```bash
cd distributed-queue
mvn clean package
java -jar target/distributed-queue-1.0-SNAPSHOT-jar-with-dependencies.jar
```

### Key Components

- **ZKDistributedQueue.java**: Queue implementation using Curator's DistributedQueue
- **DistributedQueueTest.java**: Test with multiple producers and consumers
- **ThreadPooledConsumer**: Demonstrates efficient message processing

## Implementation Details

### Cluster Node Monitoring

The monitoring system uses ZooKeeper's four-letter commands to retrieve node status:

1. **State Retrieval**: Sends the `stat` command over TCP to each ZooKeeper node
2. **Metrics Parsing**: Extracts key information about mode, connections, and latency
3. **Real-time Updating**: Uses a ScheduledExecutorService to periodically update node statuses
4. **Visualization**: Provides an HTML dashboard with color-coded status indicators

### Distributed Locking

The locking system ensures mutual exclusion across distributed processes:

1. **Lock Acquisition**: Uses Curator's InterProcessMutex to create ephemeral znodes
2. **Timeout Handling**: Supports timeouts to prevent indefinite blocking
3. **Race Condition Demo**: Shows how distributed locking prevents corruption in shared counters
4. **Clean Resource Management**: Implements AutoCloseable for reliable resource cleanup

### Distributed Queues

The queue system enables reliable, distributed messaging:

1. **Message Serialization**: Custom serialization for queue items
2. **Producer-Consumer Pattern**: Multiple producers can add to queue; multiple consumers can process
3. **Concurrency Handling**: Thread-safe operations for multi-threaded environments
4. **Event-Based Processing**: Consumers receive callbacks when new items are available

## Fault Tolerance Considerations

- The monitoring system detects node failures through connection timeouts
- Distributed locks automatically release if a holding process crashes (due to ZooKeeper's ephemeral znodes)
- Distributed queues ensure each message is processed exactly once, even during failures

## Performance Optimizations

- Connection reuse to minimize ZooKeeper session overhead
- Efficient serialization for queue messages
- Event-driven processing rather than polling
- Thread pools for handling high concurrency



## Authors

Edison Zyberaj
