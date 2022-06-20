# Introduction
## Key Features of Druid
1. Columnar Storage
2. Scalable distributed system
3. Massively parallel processing
4. realtime or batch ingestion
5. self-healing, self balance
6. cloud native, fault tolerant
7. indexes for quick filtering
8. time-based partitioning
9. Approximate algorithm
10. Automatic summarization at ingest time

## When to use
1. high insert rate but updates less common
2. reporting queries (group by)
3. latency: 100ms - few seconds
4. data has a time component
5. more than 1 table
6. high cardinality data columns: 
7. load data from Kafka, HDFS, flat files ...
8. low latency updates of existing record
9. want to do big joins

# Design
## Processes and Servers
1. process types:
- Coordinator: manage data availability
- Overload: assign data ingestion workload
- Broker: handle external queries
- Router(optional): route requests to Broker, Coordinators and Overloads
- Historical: store data
- MiddleManager: ingest data

2. Server types:
- Master:  Coordinator + Overload
- Query: Broker + Router
- Data: Historical + MiddleManager

## External Dependencies
1. Deep storage
- Shared file storage acessible by every Druid server.
- As a backup of data
- Historical processes cache dagta to offer the best latencies
- Need enough disk space
- Druid bootstrap from deep storage if every signal data server is lost and reprovisioned.
2. Metadata storage
- in a clustered deployment: Is tranditional RDBMS (PostgreSQL or MySQL)
- In a single-server deployment: Apache Derby database.
3. ZooKeeper: 
- internal service discovery
- coordination
- leader election

## Diagram
![image](https://user-images.githubusercontent.com/33732707/174683210-4860c665-d548-4c97-a62c-efaddea33a0a.png)
