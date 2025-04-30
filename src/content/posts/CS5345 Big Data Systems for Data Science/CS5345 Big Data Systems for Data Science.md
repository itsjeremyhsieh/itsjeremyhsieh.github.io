---
title: "[NUS CS5345] Big Data Systems for Data Science Lecture Note"
published: 2025-05-01
description: "Complete Note for NUS CS5345 Big Data Systems for Data Science"
tags: ["NUS", "Big Data Systems for Data Science"]
category: CS5345
draft: false
---

- [L1 Introduction](#l1-introduction)
    - [Challenges of Big Data: the 4 ‘V’s](#challenges-of-big-data-the-4-vs)
    - [Virtuous Product Cycle](#virtuous-product-cycle)
    - [Infrastructure: Cloud Computing (Utility Computing)](#infrastructure-cloud-computing-utility-computing)
    - [Service Types](#service-types)
- [L2 Principles of Big Data Systems](#l2-principles-of-big-data-systems)
  - [Datacenter](#datacenter)
    - [The 4 Big Ideas](#the-4-big-ideas)
    - [Abstractions for big data systems](#abstractions-for-big-data-systems)
- [L3 MapReduce](#l3-mapreduce)
    - [Partition](#partition)
    - [Combiner](#combiner)
    - [Performance Guidelines for Basic Algorithmic Design](#performance-guidelines-for-basic-algorithmic-design)
- [L4 MapReduce \& Databases](#l4-mapreduce--databases)
  - [HDFS assumptions](#hdfs-assumptions)
  - [Design decisions](#design-decisions)
  - [HDFS architecture](#hdfs-architecture)
    - [Namenode (Master node) responsibilities](#namenode-master-node-responsibilities)
  - [Relational Database](#relational-database)
- [L5 MapReduce \& Data Mining](#l5-mapreduce--data-mining)
  - [Similarity search](#similarity-search)
    - [Measures](#measures)
    - [Finding similar documents](#finding-similar-documents)
    - [Steps](#steps)
    - [MapReduce implementation](#mapreduce-implementation)
  - [Clustering](#clustering)
    - [K-means algorithm](#k-means-algorithm)
- [L6 NoSQL \& Distributed databases](#l6-nosql--distributed-databases)
    - [Types](#types)
- [L7 Sparks I](#l7-sparks-i)
  - [Motivation](#motivation)
  - [Spark Components and API Stack](#spark-components-and-api-stack)
  - [Spark Architecture](#spark-architecture)
  - [Evolution of Spark APIs](#evolution-of-spark-apis)
  - [RDD](#rdd)
    - [Distributed Processing](#distributed-processing)
  - [Caching](#caching)
  - [DAG](#dag)
    - [Narrow \& Wide dependencies](#narrow--wide-dependencies)
    - [Fault Tolerance and Lineage](#fault-tolerance-and-lineage)
  - [DataFrame](#dataframe)
  - [DataSets](#datasets)
- [L8 Spark II](#l8-spark-ii)
  - [Spark SQL](#spark-sql)
    - [RDD vs. DataFrame](#rdd-vs-dataframe)
    - [The Catalyst（催化劑） Optimizer](#the-catalyst催化劑-optimizer)
    - [Project Tungsten](#project-tungsten)
  - [Machine Learning with Spark ML](#machine-learning-with-spark-ml)
    - [Data Preprocessing](#data-preprocessing)
    - [Training \& Testing](#training--testing)
    - [Evaluation](#evaluation)
    - [Pipeline](#pipeline)
- [L9 Stream Processing](#l9-stream-processing)
    - [Stateful Stream Process](#stateful-stream-process)
  - [Spark](#spark)
    - [Micro-Batch Stream Processing](#micro-batch-stream-processing)
    - [Stateless Transformation](#stateless-transformation)
    - [Stateful Transformation](#stateful-transformation)
    - [Performance Tuning](#performance-tuning)
- [L10 Graph \& PageRank](#l10-graph--pagerank)
  - [PageRank](#pagerank)
    - [Flow Formulation](#flow-formulation)
    - [Random Walk Formulation](#random-walk-formulation)
  - [Three questions](#three-questions)
  - [Topic Sensitive PageRank](#topic-sensitive-pagerank)
  - [PageRank Implementation](#pagerank-implementation)
    - [Pregel: Computational Model](#pregel-computational-model)
    - [Example: computing max value](#example-computing-max-value)
    - [Pregel: Implementation](#pregel-implementation)


# L1 Introduction

Data Science: an interdisciplinary field about processes and systems to extract knowledge or insights from data in various forms.

### Challenges of Big Data: the 4 ‘V’s

- Volume (size): the scale of data
    - challenges: performance, cost, reliability, algorithm design complexity
- Velocity (速度): the speed of new data (streaming data)
    - challenges: performance, cost, reliability, algorithm design complexity
- Variety（多樣性）: the format of data
    - data integration, multi-model learning
- Veracity（真實性）: accuracy of data
    - dirty, noisy data, data uncertainty, data quality

### Virtuous Product Cycle

![image.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/image.png)

### Infrastructure: Cloud Computing (Utility Computing)

- pay-as-you-go
- dynamically provision virtual machines
- scalability (infinite capacity), elasticity (scale up or down on demand)

![image.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/image%201.png)

- **Virtual Machines**: enable sharing of hardware resources by running each application in an **isolated VM** (high overhead as each VM has its own OS)
- **Containers**: enable **lightweight** sharing of resources, applications run in an isolated way, but they **share the same OS**

### Service Types

- Infrastructure as a Service (IaaS): Utility Computing
    - user rents a virtual machine
    - e.g. Amazon EC2, Google Compute Engine, Rackspace
- Platform as a Service (PaaS)
    - provides hosting for web applications and takes care of the hardware
    - e.g. Google App Engine. User provides their web app
- Software as a Service (SaaS)
    - user doesn’t write code, just use an existing app
    - e.g. Gmail, Dropbox, Zoom

# L2 Principles of Big Data Systems

## Datacenter

![image.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/image%202.png)

- **Bandwidth**: **maximum amount** of data that can be transmitted per unit time (e.g. in GB/s) (important if **large amount** of data)
    - Throughput: the rate at which some data is **actually** transmitted
    - Bandwidth along the path: the **minimum** (bottleneck)
- **Latency**: **time taken** for 1 packet to go from source to destination (*one-way*) or from source to destination back to source (*round trip*), e.g. in ms (important when small amount of data)
    - Latency along the path: can be **added** (the sum of the latency caused in all components in the data access)
- Examples:
    - Sending data from server A’S DRAM to its own DRAM
        - Path: Local DRAM
        - Latency: $100ns$
        - Bandwidth: $20GB/s$
    - Sending data from server A’s DRAM to its own disk
        - Path: Local DRAM → Local Disk
        - Latency: $100ns + 10ms$
        - Bandwidth: $200MB/s$
    - Sending data from server A’s DRAM to the DRAM of another server (B) in the same rack
        - Path: Local DRAM → Rack Switch → Server B’s DRAM
        - Latency: $100ns \times 2 + 300$ $\mu s$
        - Bandwidth: $100 MB/s$
    - Sending data from server A’s DRAM to the DRAM of another server (C) in a different rack
        - Path: Local DRAM → Rach Switch → Datacenter Switch → Server C’s Rack Switch → Server C’s DRAM
        - Latency: $100ns \times 2 + 300 \mu s \times 2 + 500 \mu s$
        - Bandwidth: $10 MB/s$
- Capacity of Storage Hierarchy
    
    ![image.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/image%203.png)
    
    - **Disk** has much higher capacity than DRAM
    - **Storage hierarchy**: capacity increases as we go from Local Server to Rack to Datacenter
    - **Disk reads** are much more expensive than DRAM (higher latency & lower bandwidth)
    - **Costs increase** over the storage hierarchy (**latency increases**, **bandwidth decreases** from Local to Rack to Datacenter)
    - Local, Rack, Datacenter **disk latency** 都差不多
    - Rack = Network switch
- Cost of moving data within a server

![image.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/image%204.png)

### The 4 Big Ideas

- Scale “OUT”, not “up”: scale horizontal（水平）, not vertical. For reliability, cost, and scalability
- Seamless scalability: design should scale linearly to # machine
- Move processing (task, program) to the data. Not move program to the program
- Process data sequentially, avoid random access: sequential access, not random access (seeks are expensive)

### Abstractions for big data systems

**Parallelization + Divide & Conquer**

Challenges:

- Machine failure
- Synchronization (can use barrier to ensure every process must stop at certain point)
- Programming difficulty (concurrency, debugging is difficult)

Abstraction:

- hide system-level details from the developers
- separate what (what to do) from how (how to do)

# L3 MapReduce

Typical big data problem:

1. Iterate over a large number of records
2. Extract something of interest from each (**Map**)
3. Shuffle and sort intermediate results (**Shuffle**)
4. Aggregate intermediate results (**Reduce**)
5. Generate final output

![image.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/image%205.png)

MapReduce handles:

- scheduling, shuffle phase, synchronization, faults

User specifies two functions

- map $(k_1, v_1) \rightarrow List(k_2, v_2)$
- reduce $(k_2, List(v_2)) \rightarrow List(k_3, v_3)$

![image.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/image%206.png)

1. **Submit**: user submits MapReduce program (code for map and reduce functions) and configuration to master node
2. **Schedule**: Master schedules resources for map and reduce tasks
3. **Read**: input files are separated into **splits** (default 128MB each). Each split needs one “**Map task**”. Workers execute 1 map task at a time
4. **Map phase**: each worker iterated over each **<key, value>** pair in the split, computes the map function on each pair
5. **Local write**: each worker writes the output of the **map function** to its own **local disk** (for reliability and capacity, compared to DRAM)
6. **Remote read**: each reduce worker is responsible for 1 or more **keys**. For each key, it reads the data it needs from the corresponding partition of **each mapper’s local disk**
7. **Reduce phase**: the reducer computes the **reduce function** on the values of each key
8. **Write**: the output of the reduce function is written to HDFS

Why 128MB?

- Too big: no parallelism
- Too small: high scheduling overhead, also become random access

Terms

- **1 map task per split**
- **1 map function call per input <key, value> pair**
- a single map task can have many map function calls
- **reduce task number: specified by the user**
- **1 reduce function call per distinct intermediate key**
- Barrier between map and reduce phases

### Partition

user can optionally implement a custom partition (the assignment of keys to reducers) to better spread out the load among reducers (e.g. skew)

- which key go to which reducer (by default determined by a hash function)

### Combiner

combiners locally aggregate output from mappers (preprocessing, mini-reducers)

- user’s responsibility to ensure combiner doesn’t affect the correctness of final output.
    - Combiner may run 0, 1 or multiple times, based on the code
    - 要保證不管跑幾次都不會影響結果（可以跑max, min，但不能跑 mean, add, minus，因為$mean(1,1,2)\neq mean((1,1), 2)$
- e.g. Map → A:1 B:1 A:1 → Combiner → A:2 B:1 → Shuffle

**Both done during the local write**

### Performance Guidelines for Basic Algorithmic Design

- Linear scalability: more nodes can do more work at the same time
- Minimize disk and network IO
- Reduce memory working set of each task/worker
    - Working set: portion of memory that is being used
    - Large working set: high memory requirements
    
    ![image.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/image%207.png)
    
    ![image.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/image%208.png)
    
    ![image.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/image%209.png)
    

# L4 MapReduce & Databases

## HDFS assumptions

- commodity hardware (so that we can scale out horizontally)
- high component failure rates
- modest number of huge files
- large sequential reads instead of random access

## Design decisions

- files stored as chunks (or blocks, by default 128MB)
- reliability through replication (1 in other machine in the same rack, 1 in other rack, far away) (by default 3)
- single master to coordinate access, keep metadata (simple centralized management)
- designed for write-once, read-many (doesn’t support modifying files, except appending)

## HDFS architecture

![image.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/image%2010.png)

![image.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/image%2011.png)

### Namenode (Master node) responsibilities

- managing the file system namespace
    - no data is moved through the namenode
- maintaining overall health
    - “heartbeat”: periodic communication with the datanodes to check they’re alive
    - block rebalancing (ensure data is distributed close to evenly)
- namenode can have backups and checkpoints (replications of namenode)

![image.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/image%2012.png)

## Relational Database

- comprised of tables
- each table represents a relation = collection of tuples (rows)
- each row consists of multiple fields
- **Projection**: SELECT * FROM table
    - Map: take in a tuple (tuple ID as key), emit new tuple with selected attributes
    - No reducer needed
- **Selection**: SELECT * FROM Table WHERE condition
    - MAP: take in a tuple (tuple ID as key), emit new tuple that meets the condition
    - No reducer needed
- **Group by** (Aggregation): SELECT *, AVG(price) FROM Table GROUP BY id
    - Map over tuples, emit <id, price>
    - Framework automatically groups these tuples by key
    - Compute average in reducer
    - Can optimize with combiners
- **Relational joins** (Inner join) (many-to-many)
    - M1: Broadcast (Map) join
        - requires one of the tables to **fit in memory**
            - all mappers store a copy of the small (hash) table
            - mappers iterate over the big table, join the records with the small table
    - M2: Reduce-side (Common) join
        - doesn’t require a table to fit in memory, but slower
            - different mappers operate on each table, and emit records, with key as the variable to join by
            - reducer: use **secondary sort** to ensure that all keys from table X arrive before table Y
                - then holds the keys from table X in memory and cross them with records from table Y
                
                ![image.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/image%2013.png)
                
                ![image.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/image%2014.png)
                
                - secondary sort: define a new “composite key” as (k1, k2)
                    - to receive values in sorted order (k2)
                    - k1 is the original key
                    - k2 is the variable we want to use to sort
                    - custom comparator: compare k1 first, then k2
                    - partitioner: needs to be customized, to **partition by k1 only**, not (k1, k2)

# L5 MapReduce & Data Mining

## Similarity search

### Measures

- define near neighbors as points that are a small distance apart
- distance measure
    - Euclidean distance $d(a,b) = \sqrt{\sum_{i=1}^D (a_i - b_i)^2}$
    - Manhattan distance $d(a,b) = \sum_{i=1}^D |a_i - b_i|$
    - Cosine similarity $s(a,b) = cos \theta = \frac {a \cdot b}{|a| \cdot |b|}$
    - **Jaccard similarity** (between sets A and B) $S_{Jaccard} (A,B) = \frac {|A \cap B|}{|A \cup B|}$
        - Jaccard distance $d_{Jaccard}(A,B) = 1 - S_{Jaccard}(A,B)$
- similarity measures: lower distance = higher similarity

### Finding similar documents

- all pairs similarity
- similarity search

### Steps

- **Shingling**: convert documents to sets of short phrases
    - k-shingle: e.g. k=2. D = ‘the cat is glad’. Sets of 2-shingles: S(D) = {’the cat’, ‘cat is’, ‘is glad’}
- **Min-Hashing**: convert these sets to short “signatures” of each document, while preserving similarity
    - signature: a block of data representing the contents of a document in a compressed way
    - documents with the same signature are candidate pairs for finding near duplicates
    
    ![image.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/image%2015.png)
    

We can measure the similarity between documents as Jaccard similarity (Similarity metric)

|  | D1 | D2 | D3 |
| --- | --- | --- | --- |
| ‘the cat’ | 1 | 1 | 0 |
| ‘cat is’ | 1 | 0 | 1 |
| … | 0 | 0 | 1 |
- but too many pairs

Use **MinHash**

- **fast approximation** to the result
- hash each column C to a small signature h(C) (integer), such that
    - h(C) is small enough to fit in RAM
    - highly similar documents usually have the **same signature**
    - if sim(A,B) is high, then with high probability **h(A) = h(B)**
- compute the minimum of these signatures in the document
- e.g. {(the cat), (cat is), (is glad)}
    - h(’the cat’) = 12, h(’cat is’) = 74, h(’is glad’) = 48
    - min(12, 74, 48) = 12
    - any document with MinHash = 12 is similar to this document
- Can extend to multiple hashes to avoid hash conflict

### MapReduce implementation

- Map
    - read over the document and extract its shingles (the bottleneck)
    - hash each shingle and take the min
    - emit <signature, document_ID>
- Reduce
    - Receive all documents with a given MinHash signature
    - generate all candidate pairs from these documents

## Clustering

Cluster should have high intra-cluster (among) similarity, and low inter-cluster (between) similarity

### K-means algorithm

1. Initialization: pick K random points as centers
2. Repeat:
    1. Assignment: assign each point to nearest cluster
    2. Update: move each cluster center to average of its assigned points
3. Stop if no assignments change
- MapReduce with **in-mapper combiner （一定會觸發，因為寫在mapper裡）**

![image.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/image%2016.png)

# L6 NoSQL & Distributed databases

**non-relational databased (unstructured, flexible)**

- horizontally scalability (volume, velocity)
- replicate data over many servers (volume, velocity)
- simple call interface
- often weaker concurrency model
- efficient use of distributed indexes and RAM
- flexible schemas (variety)

### Types

- **Key-value stores**
    - stores **associations between keys and values**
    - **keys** are primitives and can be queries
    - **values** can be primitive or complex, usually **cannot be queries**
    - vary **simple** API (Get, Put)
    - suitable for small continuous r/w, basic info
    - non-persistent: just a big in-memory hash table (Memcached, Redic)
    - persistent: data stored to disk (RocksDB, Dynamo, Riak)
- Document Stores
    
    ![image.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/image%2017.png)
    
    - collections (tables) have multiple documents (rows)
    - a document is a JSON-like object (fields and values)
    - different documents can have different fields (can also be nested)
    - allow some **querying based on the content** (CRUD)
    - e.g. MongoDB, CouchDB
- Graph databases
    - records relationships
    - e.g. Neo4j, Hyper GraphDB
- Vector databases
    - store vectors (each row represents a point in d dimensions)
    - allow fast **similarity search**
    - scalability, real-time updates, replication
    - popular due to AI / ML
    - e.g. Milvus, Redis, Weaviate, MongoDB Atlas

# L7 Sparks I

## Motivation

- Hadoop MapReduce:
    - intermediate data has to be written to **local disks**, which is slow (network & disk I/O costs)
    - not suitable for iterative processing, as each individual step has to be modeled as a MapReduce job
- Spark:
    - most of the intermediate results stored in **memory**, faster for iterative processing. (may still spill to disk)
    - Ease of programmability

## Spark Components and API Stack

![image.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/image%2018.png)

## Spark Architecture

- **Driver Process** responds to user input and distributes work to Executors
- **Executors** run the code assigned to them and send the results back to the driver
- **Cluster Manager** allocates resources when the application requests it

![image.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/image%2019.png)

## Evolution of Spark APIs

![image.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/image%2020.png)

## RDD

**Resilient Distributed Datasets (RDDs, 彈性分散式資料集)**

![image.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/image%2021.png)

- Resilient: Achieve **fault tolerance** by lineage (DAG)
    - HDFS uses 3 replicas
- Distributed Datasets: Represent a collection of objects (records) that is **distributed** over machines
- RDDs are immutable (cannot change once created)

**Transformations**

![image.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/image%2022.png)

- transforming RDDs into another RDDs (modification)
- **lazy**, will not be executed until an **action** is called on it
    - advantages are that Spark can optimize the query plan to improve speed
- e.g. **map, order, groupBy, filter, join, select**

**Actions**

![image.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/image%2023.png)

- trigger Spark to compute a result from a series of transformations
- collect(): one of the action. Ask Spark to retrieve all elements of the RDD to the driver node.
- e.g. **show, count, save, collect**

### Distributed Processing

- transformations and actions are executed in parallel (in workers). The results are only sent to the driver in **the final step**

![image.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/image%2024.png)

## Caching

![image.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/image%2025.png)

- cache(): saves an RDD to memory of the worker node
- persist(options): can be used to save an RDD to memory, **disk,** or off-heap memory
- cache used when it is expensive to compute and needs to be **re-used multiple times**
- if worker nodes have not enough memory, they will evict (delete) the least recently used RDDs

## DAG

Directed Acyclic Graph

- Spark creates a graph which represents all the RDD objects and how they will be transformed
- Transformations construct this graph, actions trigger computations on it

![image.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/image%2026.png)

![image.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/image%2027.png)

### Narrow & Wide dependencies

- **Narrow dependences**: each partition of the parent RDD is used only by 1 partition of the child RDD
    - grouped within the same stage
    - e.g. **map, flatMap, filter, contains**
- **Wide dependencies:**  each partition of the parent RDD is used by multiple of the child RDD
    - across stages, data needs to be **shuffled** (exchanged across partitions), involves writing intermediate results to disk, slower
    - should minimize shuffling
    - e.g. **reduceByKey, groupBy, orderBy**

### Fault Tolerance and Lineage

- Spark tries to store all the data in **memory**, so cannot duplicate all data ⇒ lazy
- If a worker node goes down, we replace it by a new worker node, and use the DAG to recompute the data in the lost partition (only need to recompute the RDDs from the lost partition)

## DataFrame

- represents **a table of data**, similar to tables in SQL or DataFrames in pandas
- **higher level** interface than RDDs
- ultimately compiled down to RDD operations by Spark
- easy way to transform DataFrames is to use **SQL queries**
    - takes in a DataFrame and returns another DataFrame
- transformation functions take in either strings or column objects, which represent columns

![image.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/image%2028.png)

## DataSets

- similar to DataFrames, but are type-safe (need to define type)
- RDD (customized function) + DF

![image.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/image%2029.png)

# L8 Spark II

## Spark SQL

- a unified stack for distributed execution
- Keep track of schema & support optimized relational operations

![image.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/image%2030.png)

![image.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/image%2031.png)

### RDD vs. DataFrame

**RDD**

- instruct Spark HOW to compute the query
- Spark doesn’t understand the structure of the data in RDD

**DataFrame**

- tell Spark WHAT to do
- more expressive, simpler
- Spark can inspect the query and optimize or arrange the operations for efficiency

![image.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/image%2032.png)

### The Catalyst（催化劑） Optimizer

- input a computational query

- converts it into an **execution plan** through 4 transformational phases
    - Analysis
    - Logical optimization
    - Physical planning
    - Code generation (Project Tungsten)
    

![image.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/image%2033.png)

### Project Tungsten

- Substantially **improve the memory and CPU efficiency**
- By:
    - Memory management
    - Cache-aware computation
    - Code generation (optimize RDD code)

## Machine Learning with Spark ML

- if use Hadoop, each iteration would be a MapReduce job, too slow (local disk write/ read)
- **Classification**: categorize samples into classes, given training data
- **Regression**: predict numeric labels (continuous), given training data

### Data Preprocessing

Missing values

- eliminate rows with missing values
- fill in the missing values (**imputation**)
    - based on mean/median
    - fitting a regression model to predict that attribute
    - Dummy variables (insert a column = 1 if variable was missing)

One Hot Encoding

- convert discrete feature to a series of binary features
- used when there’s no ordinal relationship among categories

### Training & Testing

Logistic regression: Sigmoid function (for **binary** classification)

![image.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/image%2034.png)

- Minimize Cost function by learning optimal weights and bias
- Parallel gradient descent
    
    ![image.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/image%2035.png)
    

### Evaluation

Classification Model:

![image.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/image%2036.png)

Regression Model:

![image.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/image%2037.png)

### Pipeline

- better code reuse
- easier to perform testing and tuning
- **Transformers**
    - for mapping DF to DF
    - e.g. one-hot encoding, tokenization
    - has a transform() method
    - a **fitted model** (e.g. logistic regression model) is a Transformer that transforms a DF into one with the predictions
- **Estimator**
    - is an **algorithm** which takes in data, outputs a fitted model
    - e.g. LogisticRegression
    - has a fit() method, which returns a Transformer

![image.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/image%2038.png)

# L9 Stream Processing

- Batch (offline) approaches: operate on full dataset, **all at once**
- **Streaming** (online) approaches: process input as it is received (**real-time**)
    - elements of the stream: tuple (each row of the df)
    - infinite stream: cannot store the entire stream accessibly (limited memory)

### Stateful Stream Process

the **intermediate record** needs to be stored, for failure recovery

hard to achieve **exact-once** (guarantee all data are processed once and only once) ⇒ need to have local state stored

periodic checkpoint to **persistent storage**

## Spark

### Micro-Batch Stream Processing

- divides data into micro batches
- each batch is processed in Spark cluster in a distributed manner
- **Advantage**
    - Quick and efficiently recover from failure
    - Deterministic nature ensures exactly-once processing
- **Disadvantages**
    - Latencies of a few seconds
- If batch too small ⇒ overhead
    - huge batch: delay ↑, throughput ↑
- easy to program, just like writing batch pipelines
- data stream as an **unbounded table**
    - new records in the data stream ⇒ new rows **appended** to an unbounded table
- 5 Steps to define a streaming query
    1. Define input source
    2. Transform data
    3. Define output sink and mode
        1. Output writing details
        2. where and how to write the output
    4. Specify processing details
        1. Triggering details
        2. Checkpoint location
    5. Start the query

![image.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/image%2039.png)

### Stateless Transformation

- process each row individually without needing any information form precious rows
- Projection operations: select(), explode(), map(), flatMap()
- Selection operations: filter(), where()

### Stateful Transformation

- DataFrame.groupBy().count()
- the incremental plan adds the count of new records to the previous count generated by precious micro-batch
- the state is maintained in the memory and is check-pointed to the configured location to tolerate failures
    
    ![image.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/image%2040.png)
    
- Aggregations not based on time
    - **Global** aggregations: runningCount = sensorReadings.groupBy().count()
    - **Grouped** aggregations: baselineValues = sensorReadings.groupBy(’sensorId’).mean(’value’)
- Time Semantics
    - **Processing Time:** the time of stream processing machine’s clock（抵達cluster，開始處理的時間）
        
        ![image.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/image%2041.png)
        
    - **Event Time** (better): the time an event actually happened（log發生的時間）
        
        ![image.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/image%2042.png)
        
        - operations based on event time are **predictable** and results are **deterministic**
        - **event time window** computation will **yield the same result** no matter how fast the stream is processed or when the events arrive at the operator (e.g. network congestion)
        - **watermark:** how long do we have to wait before we can be certain that we have received all events that happened before a certain point of time

![image.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/image%2043.png)

![image.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/image%2044.png)

![image.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/image%2045.png)

![image.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/image%2046.png)

- watermark：目前已知的最大 event time 減去設定的延遲時間
    
    **12:14 - 10 分鐘 = 12:04**
    
    - 表示事件時間（eventTime）在 **12:04 之前的資料將會被認為是「太遲了」**（即使它剛到，也會被丟棄）
- 檢測log是否還會被記錄到result table
    - result table 中所有 window.end **≤** watermark 的資料會被封存，不再更新，晚到的 log 即使到達也不會被記錄

### Performance Tuning

- Cluster resource provisioning to run 24/7
- Number of partitions
- Set source rate limits (set max to avoid congestion)
- Multiple streaming queries (so no waste of Spark resources)
- Event-driven streaming application
    - Tradition warehouse have high computational cost on ETS process → bottleneck
    - Event log:
        - decouple sender/ receiver/ and streaming processing
        - provides asynchronous, nonblocking event transfer
    - KafKa（事件排隊）
        - open-source distributed event streaming platform
            
            ![Screenshot 2025-03-29 at 10.58.23 PM.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/Screenshot_2025-03-29_at_10.58.23_PM.png)
            
    - Tradition warehouse have high computational cost on ETL process 
    **（Extract, Transform, Load）**
    - a streaming process takes care of all the processing steps ⇒ much shorter time needed
    
    ## Flink Architecture
    
- **Flink** is a distributed system for **stateful** parallel data streaming processing（事件流處理）
    
    ![Screenshot 2025-03-29 at 11.01.22 PM.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/Screenshot_2025-03-29_at_11.01.22_PM.png)
    
- **Task manager**
    - execute several tasks at the same time
    - offers a certain number of **processing slots** to control the number of
    tasks it is able to concurrently execute
    - Data transfer
        - the tasks of a running application are continuously exchanging data
        - the TaskManagers take care of shipping data
        - collects records in buffers before they are shipped
- **Processing slot**
    - can execute one slice of an application—one parallel task of each
    operator of the application
        
        ![Screenshot 2025-03-29 at 11.03.19 PM.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/Screenshot_2025-03-29_at_11.03.19_PM.png)
        
    
    ## Event Time Processing
    
- **Timestamp**: every record must have an event timestamp
- **Watermarks**
    - used to derive the current event time at each task in an event-time application
    - special records holding a timestamp as a Long value. Watermarks flow in a stream of regular records with annotated timestamps
    - 幫助系統知道“目前”事件的進度，
    - 事件時間的一個標記，表示所有事件時間**小於**該水印的事件應該已經到達並處理過。
    - can trigger results early/ on-time or late ⇒ real-time triggers
        
        ![Screenshot 2025-03-29 at 11.07.07 PM.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/Screenshot_2025-03-29_at_11.07.07_PM.png)
        
        ![Screenshot 2025-03-29 at 11.15.43 PM.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/Screenshot_2025-03-29_at_11.15.43_PM.png)
        
    - Local state management
        - each parallel task locally maintains its state in memory to ensure fast state
        accesses
    - **Checkpoint**
        - for failure recovery
        - Checkpointing the state of a task to a remote and persistent storage
        - Method 1: **Stop the World**
            1. Pause the ingestion of all input streams
            2. Wait for all in-flight data to be completely processed, meaning all tasks
            have processed all their input data.
            3. Take a checkpoint by copying the state of each task to a remote,
            persistent storage. The checkpoint is complete when all tasks have
            finished their copies.
            4. Resume the ingestion of all streams.
            - Issue:
                - every time checkpoint happens: all process must top ⇒ delay
                - failure recovery ⇒ resets all the states from latest checkpoint
                
                ![Screenshot 2025-03-29 at 11.20.41 PM.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/Screenshot_2025-03-29_at_11.20.41_PM.png)
                
        - Method 2: **Distributed checkpoint (checkpoint barrier)**
            - based on the Chandy–Lamport algorithm for distributed snapshots
            - does not pause the complete application but decouples checkpointing
            from processing
            - injected by source operators, have ID
            1. Job Manager initiate checkpoint by sending a message to all the source
            2. Sources checkpoint their state and emit a checkpoint barrier
                1. also tell job manager checkpoint is done at source 1 & 2
            3. Tasks wait to receive a barrier on each input partition
                1. Records from input streams for which a barrier already arrived
                are **buffered**
                2. 如果 source A 已經收到 checkpoint barrier 了，那正在排隊等 source B 處理的 job 會先 buffer 起來，等待 source B 也收到 checkpoint barrier 後再繼續
                    
                    ![Screenshot 2025-03-29 at 11.27.53 PM.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/Screenshot_2025-03-29_at_11.27.53_PM.png)
                    
                - checkpoint barrier 到哪裡就 checkpoint 當下的state
                - a checkpoint is complete when all tasks have acknowledged the
                successful checkpointing of their state
        
        ## Comparison
        

![Screenshot 2025-03-29 at 11.29.04 PM.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/Screenshot_2025-03-29_at_11.29.04_PM.png)

# L10 Graph & PageRank

Graph = Nodes (objects) + Edges (relationships, directed or undirected)

## PageRank

- Web as a **directed graph**
    - Nodes: webpages
    - Edges: hyperlinks
- Rank pages on the web
    - measure the **importance of pages**
- Idea: Links as votes
    - Pages is more important if it has more **in-links** (other website referring to you)
    - Problem: may make dummy web page to link to their own page
    - Solution: **make the number of ‘votes’ proportional to its own importance**.
        - Links from important pages count more

### Flow Formulation

- **Voting Formulation**
    - If page $j$ with importance $r_j$ has $n$ out-links, each link gets $\frac{r_j}{n}$ votes
    - Incoming votes: $r_j = \frac {r_i}{3} + \frac {r_k}{4}$
    - $r_j = \sum_{i \rightarrow j} \frac {r_i}{d_i}$, $d_i$ = number of out-links
        
        ![Screenshot 2025-04-05 at 11.56.35 PM.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/Screenshot_2025-04-05_at_11.56.35_PM.png)
        
        ![Screenshot 2025-04-05 at 11.59.04 PM.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/Screenshot_2025-04-05_at_11.59.04_PM.png)
        
    - No unique solution
    - ⇒ Addition constraint to force uniqueness: $r_y + r_a + r_m = 1$
    - Work only for **small examples**
- **Matrix Formulation**
    - Stochastic adjacency matrix M
        - Let page $i$ has $d_i$ out-links
        - If $i \rightarrow j$, then $M_{ji} = \frac{1}{d_i}$ else $0$
            - M is a column stochastic matrix (column sum = 1)
    - Rank vector $r$: vector with an entry per page
        - $r_i$ is the importance score of page $i$
        - $\sum_i r_i =1$
        - $r=M \times r$
- Two formulations are equivalent.
    
    ![Screenshot 2025-04-06 at 12.04.13 AM.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/Screenshot_2025-04-06_at_12.04.13_AM.png)
    
- Solve using **Power Iteration**
    
    ![Screenshot 2025-04-06 at 12.05.04 AM.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/Screenshot_2025-04-06_at_12.05.04_AM.png)
    

### Random Walk Formulation

- At time $t=0$, surfer starts on a random page
- At any time $t$, surfer is on some page $i$
- At time $t+1$, the surfer follows an out-link from $i$ uniformly at random
- Process repeats indefinitely
- $p(t)$ will converge eventually
- $p(t+1) = M \times p(t)$

![image.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/image%2047.png)

![image.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/image%2048.png)

## Three questions

- Does it converge?
    - Not always

- Does it converge to what we want?
    - Some pages are **dead ends** (**no out-links**)
        - Random walk has “nowhere” to go
        - Such pages cause importance to “leak out”
            
            ![Screenshot 2025-04-06 at 12.36.53 AM.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/Screenshot_2025-04-06_at_12.36.53_AM.png)
            
    - **Spider traps** (**all out-links are within the group**)
        - Random walk gets “stuck” in a trap
        - Eventually absorb all importance
            
            ![Screenshot 2025-04-06 at 12.33.56 AM.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/Screenshot_2025-04-06_at_12.33.56_AM.png)
            

![Screenshot 2025-04-06 at 12.32.52 AM.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/Screenshot_2025-04-06_at_12.32.52_AM.png)

- Solution: **Teleports**
    - At each step, the random surfer has two options
        - with prob. $\beta$, follow a link at random
        - with prob. $1-\beta$, jump to some random page
        - $\beta$ are in the range 0.8 ~ 0.9
    - If at **Dead End**, always teleport
        
        ![Screenshot 2025-04-06 at 12.37.37 AM.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/Screenshot_2025-04-06_at_12.37.37_AM.png)
        
    
    ![Screenshot 2025-04-06 at 12.38.29 AM.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/Screenshot_2025-04-06_at_12.38.29_AM.png)
    
- Problem with PageRank
    - Measures generic popularity of a page
        - Doesn’t consider popularity based on specific topics
        - Solution: Topic-Specific PageRank
    - Uses a single measure of importance
        - Other models of importance
        - Solution: Hubs-and-Authorities
    - Susceptible to Link spam
        - Artificial link topographies created in order to boost page rank
        - Solution: TrustRank

## Topic Sensitive PageRank

- Instead of generic popularity, we measure **popularity within a topic**
- Allows search queries to be answered based on interests of the user
- When teleport:
    - **Standard PageRank**: can go to any page with equal probability
    - **Topic Specific PageRank**: can only go to a topic-specific set of relevant pages (teleport set)
- Idea: Bias the random walk
    
    ![image.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/image%2049.png)
    
    ![Screenshot 2025-04-11 at 11.21.39 AM.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/Screenshot_2025-04-11_at_11.21.39_AM.png)
    
    - weight all pages in the teleport set $S$ equally
- Compute as for regular PageRank:
    - Multiply by $M$, then add a vector
- Discovering the Topic Vector $S$
    - Create different PageRanks for different topics

## PageRank Implementation

- Graph Algorithms
    - **Local computations** at each vertex
    - **Passing messages** to other vertex
- User only implements **compute()**, that describes the algorithm’s behavior **at one vertex, in one step**

### Pregel: Computational Model

- Computation consists of a series of **supersteps**
- In each **superstep**, the framework invokes a user-defined function, compute(), for each vertex (work in parallel)
- **compute()**
    - specifies behavior at a single vertex
    - it can:
        - **read messages** sent to $v$ in superstep $s-1$
        - **send messages** to other vertices that will be read in superstep $s+1$
        - **read or write the value of $v$** and the value of its outgoing edges
- Termination
    - a vertex can choose to deactivate itself
    - is woken up if new messages received
    - computation stops when all vertices are inactive

### Example: computing max value

![Screenshot 2025-04-11 at 11.33.39 AM.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/Screenshot_2025-04-11_at_11.33.39_AM.png)

### Pregel: Implementation

- Master & Workers architecture
    - Vertices are hash partitioned and assigned to workers
    - Each worker maintains the state of its portion of the graph in memory
    - Computations happen in memory
    - In each superstep, each worker loops through its vertices and executes compute() (work in parallel)
    - Messages are sent from vertices
        - to vertices on the same worker
        - to vertices on different workers (**buffered locally** and send as batch to reduce network traffic)
- Fault tolerance
    - Checkpointing to persistent storage
    - Failture detected through heartbeats
    - Corrupt workers are reassigned and reloaded from checkpoints

![Screenshot 2025-04-11 at 11.44.04 AM.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/Screenshot_2025-04-11_at_11.44.04_AM.png)

![Screenshot 2025-04-11 at 11.44.30 AM.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/Screenshot_2025-04-11_at_11.44.30_AM.png)

- Other Graph Processing Projects
    - Giraph: open-source implementation of Pregel by Facebook
    - Spark GraphX/ GraphFrame
        - Extends RDDs to Resilient Distributed Property Graphs
        - Join Vertex Table & Edge Table to capture relationships
            
            ![Screenshot 2025-04-11 at 11.47.01 AM.png](CS5345%20Big%20Data%20Systems%20for%20Data%20Science%201a172aee799e806594f6d64b96bcbbd4/Screenshot_2025-04-11_at_11.47.01_AM.png)
            
    - Neo4j
        - Graph database + processing
        - SQL like interface