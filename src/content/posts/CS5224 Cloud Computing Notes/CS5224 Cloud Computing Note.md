---
title: CS5224 Cloud Computing
published: 2025-04-16
description: "Complete Note for NUS CS5224 Cloud Computing"
tags: ["NUS", "Cloud Computing"]
category: CS5224
draft: false
---

# L1 Introduction

## What is Cloud Computing?

**On-demand service** + **Elastic resource**

- Program is an Internet (cloud) service, and platform are datacenters

## History

- 1996: First time name appeared.“ Cloud computing” was coined at Compaq Computer
- 1999: [Salesforce.com](http://Salesforce.com) pioneered the concept of delivering enterprise applications via a simple website
- 2002: AWS
- 2006: AWS S3, EC2, Google App Engine
- 2009: Microsoft Azure

## Why?

Improves **availability** and **elasticity（彈性）**

Reduces business **cost**

## Key terms

- Elastic resource
- Availability
- Capacity planning (Resource provisioning):
    - determine and fulfill **future demands** of IT resources
    - Strategies:
        - Lead strategy: add capacity **in anticipation** of demand (before)
        - Lag strategy: add capacity **when** resource **reach its full capacity**
        - Match strategy: add capacity **in small increments** as demand increases
    - Should avoid under-provisioning & over-provisioning
    - If on-premise:
        - IT department is a big cost
        - **Up-front** investment costs + **operational** costs
    - Organizational Agility（敏捷）
        - organization’s responsiveness to change
        - On-premise: costs may be prohibitive（令人卻步的）
        - Cloud: Elastic IT resources
- Cloud based IT resources
- Scaling (horizontal, vertical):
    - Horizontal: add same resource type (水平) e.g. add more servers
    - Vertical: replace resource with higher/ lower capacity in a single node. (垂直) e.g. add an CPU to the server
    - Horizontal easier/less expensive than vertical
- Cloud services:
    - IaaS
    - PaaS
    - SaaS
- Overlapping of Trust Boundaries (not secure, increase security vulnerabilities)
    - Expansion of trust boundary: from own (own data $\rightarrow$ own data + other company’s data on the same cloud)
        
        ![image.png](CS5224%20Cloud%20Computing%201a072aee799e80999bc3dd42f7d5e144/image.png)
        
- Reduced operational governance control, privacy/ legal issues
    - Data localization/ residency

# L2 Concepts & Models

NIST 5 Essential Characteristics, 4 Deployment Models, 3 Service Models

## 5 Essential Characteristics

- On-demand (elasticity) self-service (via a service portal)
- Broad network access: ubiquitous access (must have good network)
- Resource pooling (management): location independent, multi-tenancy (多租戶)
    - Multi-tenancy: An instance of the program serves multiple cloud consumers (by virtualization)
- Rapid elasticity: time to market/ fast deployment
- Measured service: pay-per-use billing

## 4 Deployment Models

- Public Cloud: available to the general public, shared by all consumers (most common)
- Private Cloud: used only by an organization (for enterprises with large scale IT) (can be on site or by a third party)
    - Amazon VPC
- Community Cloud: shared by multiple organization based on common operational and regulatory requirements
    - modified form of a private cloud
- Hybrid (Federated) Cloud: 2 or more public and private clouds that interoperate (flexibility)
- Multi-cloud: using two or more clouds from different cloud providers
- Sovereign Cloud (主權): Meet regulatory and compliance needs of a country, data only stays within national borders, prevents foreign access

## 3 Service Models

- **Different levels of abstraction and management**
- SaaS: uses provider’s application over a network/ browser (e.g. Google apps)
    - user remotely runs the software provided on the cloud
    - provider installs and maintains software
    - highest level of abstraction
- PaaS: deploy customer-related applications to a cloud (e.g. Google’s app engine, Amazon AWS, Microsoft Azure)
    - Platform software + computing resources
- IaaS: Rent processing, storage, network capacity … (e.g. Amazon EC2, DigitalOcean)
    - Lowest level of abstraction (only abstract physical resources)

![image.png](CS5224%20Cloud%20Computing%201a072aee799e80999bc3dd42f7d5e144/image%201.png)

![image.png](CS5224%20Cloud%20Computing%201a072aee799e80999bc3dd42f7d5e144/image%202.png)

## Cloud Computing Reference Architecture

![image.png](CS5224%20Cloud%20Computing%201a072aee799e80999bc3dd42f7d5e144/image%203.png)

### 5 major actors

- Cloud consumer
- Cloud provider: 3 types: IaaS, PaaS, SaaS cloud provider
- Cloud auditor: conducts independent assessment of cloud services (稽查員)
    - verify compliance with regulation and security policy
- Cloud broker (marketplace): negotiate the relationships between providers and consumers
    - Service intermediation: provides value-added services
    - Service aggregation: combines and integrated multiple services into one or more new services, data integration,
        - ensure secure data movement between cloud consumers and multiple cloud providers
    - Service arbitrage: flexibility to choose services from multiple agencies
- Cloud carrier: provides connectivity and transport of cloud services from providers to consumers

### 3 layers of service orchestration

![image.png](CS5224%20Cloud%20Computing%201a072aee799e80999bc3dd42f7d5e144/image%204.png)

# L3 Cloud Architecture

## Resource Organization

- to achieve **elasticity** and **scaling**
- to achieve **balanced** utilization of cloud resources

### Workload Distribution Architecture

- distribute workload over available cloud resources $\rightarrow$  use horizontal scaling (add more machines)
- **Load Balancer** distributes workload
    - greater fault tolerance
    - elastic load balancing: automatically distributes incoming traffic
    - Types:
        - Application LB: single point of contact for clients, contains one or more listeners
            - Listener:
                - checks for requests from clients using protocol & port defined
                - user-defined rules, consist of priority, one or more actions, one or more conditions, default rule is necessary
            - Steps:
                1. LB receives a request
                2. Request is evaluated and appropriate rule is applied 
                3. Select a target from the target group for the rule action
        - Network LB
        - Gateway LB
        - Classic LB

### Resource Pooling Architecture

- aggregate cloud resources of different types to serve diverse needs
- maintain identical IT resources to ensure synced
- Types:
    - Dedicated Pools: becomes complex
        - CPU pool, memory pool, storage pool, network pool…
    - Sibling Resource Pools:
        - drawn from physical grouped IT resources and not spread out across data centers
        - isolated from one another so each cloud consumer is given access to its respective pool
            
            ![image.png](CS5224%20Cloud%20Computing%201a072aee799e80999bc3dd42f7d5e144/image%205.png)
            
    - Nested Resource Pool:
        - larger pools divided into smaller pools with same type of IT resources
            
            ![image.png](CS5224%20Cloud%20Computing%201a072aee799e80999bc3dd42f7d5e144/image%206.png)
            

### Dynamic Scalability Architecture

- enable variable resource utilization to **meet usage demand fluctuations**, based on **predefined** scaling conditions
- Types:
    - Dynamic **Horizontal** scaling: same resource instance scaled dynamically
    - Dynamic **Vertical** scaling: scale processing capacity of a single IT resource
    - Dynamic **Relocation**: **resource relocated** to host with larger capacity
- Steps
    1. Automated scaling listener monitors capacity
    2. Listener takes decision baed on predefined scaling policy
    3. Listener replicates resources

### Elastic Resource Capacity Architecture

- dynamic provisioning of virtual servers
- allocated and reclaims CPUs, RAMs, etc.
- runtime virtual server is monitored
- additional resources leveraged **before capacity threshold reached**
- virtual server and IT resources **vertically** scaled
- use **Intelligence Automation Engine**

![image.png](CS5224%20Cloud%20Computing%201a072aee799e80999bc3dd42f7d5e144/image%207.png)

![image.png](CS5224%20Cloud%20Computing%201a072aee799e80999bc3dd42f7d5e144/image%208.png)

### Service Load Balancing Architecture

- redundant cloud service deployments are created with a load balancer to dynamically distribute workloads
- resource pool $\longleftrightarrow$ duplicated cloud service
- Types:
    - **Independent** of cloud services and their host servers
    - **Build-In** to cloud service as part of application or server environment

### Scaling On-Premise Provisioning

- move things to the cloud if needed $\rightarrow$ hybrid
- **Burst-Out:** to meet higher usage demand, scale to cloud
- **Burst-In**: on lower demand, revert back to on-premise

# L4 Resource Hosting & Datacenter

- **Latency**: **Time takes** a packet to travel from one node to another. Important when **small amount** of data transfer, response time is key
- Bandwidth: # bits transferred per unit time. Important when **large amount** of data transfer

Datacenter components:

- **main server hall:** compute, storage, network
    - Use commodity hardware with modular architecture
    - servers $\rightarrow$ server racks $\rightarrow$  datacenter
    - Rack: supports servers, storage & network equipment
        - **Top of Rack (TOR)**: has provision for power, battery & rack-level switch (connects IT resources within rack & rack to the datacenter)
        - Rack-level switch: connects IT resources within rack and connects the rack to the datacenter network
- mechanical yard: host **cooling** system
- electrical yard: generators and power distribution

Storage:

- Types
    - **Private** to individual running tasks: in local DRAM or disk
    - **Shared state** of distributed workload

Network:

- **Local Area Network (LAN)**: within a rack, redundant connectivity
- **Storage Area Network (SAN)**: between servers and storage systems

Electricity Demand

- from air to **water:** water is 1000x more efficient than air
- **Power Usage Efficiency (PUE)**: total power used by datacenter / power used by IT equipment (ideal **PUE= 1**)
- Energy Proportional Systems: energy efficiency is not a linear function
    - Energy Proportional Systems consumes almost no power when idle, very little under light load, more power as load increases

Tiers:

![image.png](CS5224%20Cloud%20Computing%201a072aee799e80999bc3dd42f7d5e144/image%209.png)

# L5 Virtualization & Multi-tenancy

### Virtualization

- Enables a single physical infrastructure to function as multiple logical infrastructure or resource
- one-to-many
- improve resource utilization through **sharing**
- Pros:
    - hardware independence
    - resource consolidation (整合): increase hardware utilization
    - resource replication (複製): rapid scaling as resources are virtual
- Cons:
    - performance overhead (performance drops)
    - single point of failure: virtualization software fails, everything fails

Types of virtualizations

- **Processor** virtualization
- **Memory** virtualization
- **Storage** virtualization
- **Network** virtualization: abstracting physical network components to form a virtual network
- **Data** virtualization: aggregates heterogeneous data from different sources to a **single** logical or virtual volume of data
- **Application** virtualization: allows users to access the virtual instance of hosted application without installation

**Operation System Protection**

![image.png](CS5224%20Cloud%20Computing%201a072aee799e80999bc3dd42f7d5e144/image%2010.png)

**Virtualization Approaches**

- Virtualization software = hypervisor = virtual machine manager (VMM)
- virtualization software (ring 0), guest OS (ring 3)
- **Full Virtualization**
    
    ![image.png](CS5224%20Cloud%20Computing%201a072aee799e80999bc3dd42f7d5e144/image%2011.png)
    
    - Can run different guest OSs
    - VMM translates guest OS instruction with **binary translation**
    - Guest OS isn’t aware that it is virtualized
    - Pros:
        - isolation & security
        - different OSs can run simultaneously
        - guest OS can easily be migrated
        - easy to install & use
    - Cons:
        - binary translation overhead
        - need correct combination of hardware & software
- **Para Virtualization**
    
    ![image.png](CS5224%20Cloud%20Computing%201a072aee799e80999bc3dd42f7d5e144/image%2012.png)
    
    - **Modified guest OS** (ring 0) replaces OS requests with **hypercalls**: systems calls with privilege to communicate directly between OS and hypervisor **without translation**
    - Guest OS knows it is running in a virtualized environment
    - Pros:
        - No need for binary translation
        - No need for special hardware
    - Cons:
        - Overhead of guest OS kernel modifications
        - Modified guest OS cannot migrate to run on physical hardware
        - difficult to migrate to other hosts
- **Hardware-Assisted Virtualization (Hybrid)**
    
    ![image.png](CS5224%20Cloud%20Computing%201a072aee799e80999bc3dd42f7d5e144/image%2013.png)
    
    - **Hardware (processor) extension** supports virtualization
    - OS requests directly **trap** the hypervisor: no need for binary translation & guest OS modification
    - 「Trap」就是 CPU 偵測到 Guest OS 執行特權指令後，會自動轉交給 Hypervisor 處理的機制。
    - VMM has the highest root privilege
- Summary

![image.png](CS5224%20Cloud%20Computing%201a072aee799e80999bc3dd42f7d5e144/image%2014.png)

**Hypervisors (**Virtualization software)

- Type 1: Bare Metal
    - runs on physical infrastructure without help from host OS
    
    ![image.png](CS5224%20Cloud%20Computing%201a072aee799e80999bc3dd42f7d5e144/image%2015.png)
    
- Type 2: Hosted (Embedded) (more common)
    - requires the help of host OS to communicate with infrastructure
    
    ![image.png](CS5224%20Cloud%20Computing%201a072aee799e80999bc3dd42f7d5e144/image%2016.png)
    

**Containers**

- shared host’s system kernel, lightweight
- **Docker (PaaS)**
    - can run an application in an isolated environment (container)
    - use namespaces to provide isolated workspace
    - use a client-server architecture
    - **Docket daemon**: building, running and distributing the docker containers, manages docker objects
    - **Docker client**: primary way users interact with Docker (REST API)
    - **Docker Desktop**: easy to install application for host OS
    - **Docker registries**: stores images
    - **Images**: read-only template with instructions for creating a Docker container
    - **Containers**: runnable instance of an image

Multi-tenancy (多租戶)

- a single instance of software runs on a server and serves multiple tenants (users)
- from cloud consumers: dedicated instance of software with customized view
- Multi-tenancy vs. Virtualization
    - Multi-tenancy: dedicated consumer instance of software
    - Virtualization: abstraction of physical resources
    - Multi-tenancy is possible because of virtualization

# L6 Applications and Paradigms

Cloud applications focus mainly on **enterprise computing**

Ideal: 

- app can partition its workload into $n$ tasks, run on n processing units to reduce execution time to $\frac{1}{n}$(run in parallel)
- can be divided into many independent tasks

Not ideal:

- complex workflow & dependencies (e.g. high-performance computing)
- intense **communication** among instances
- workload cannot be partitioned

Challenges:

- Consumer: scale application to accommodate dynamic load, system failure, checkpoint
- Provider: manage a large number of systems, QoS guarantee
- Performance isolation, reliability (can be solved with redundancy, backup/ checkpoint), Latency/ Bandwidth fluctuations
- Data Logging: necessary, but need to limit it

## Architecture

- Relies on Internet and web technology: high accessibility
- Use web technology as implementation medium & management interface
- Use web-based client-server architecture
- Three-tier architecture
    - Presentation layer: the UI, sits on both the **client and server side**
    - Application layer: the logic, sits on **server side** to receive client requests and generates web content based on application logic
    - Data layer: persistent data stores, sits on **server side**, interact with application servers and the databases

![image.png](CS5224%20Cloud%20Computing%201a072aee799e80999bc3dd42f7d5e144/image%2017.png)

**Simple Object Access Protocol (SOAP)**

- application protocol for web applications
- common web service messaging format
    - XML (structured), uses TCP or UDP as transport protocol

**Representational State Transfer (REST)**

- Software architecture for distributed hypermedia systems
- supports client communication with **stateless** servers
- uses JSON
- Platform & language independent
- Uniform interface, client-server decoupling
- Stateless (doesn’t record the states, so must include state info in the request)
- Cacheability
- Layered system architecture
- Code on demand (the return can be code and be run)

### Web Services for Cloud Applications

Cloud services are **distributed**: single service request might require message passing between multiple servers

Accessed by different clients with different languages, so requires a **standard interface** for communication

Web service APIs implement a **standard communication interface (REST API)**

## Cloud Application Development Models

### IaaS

- Web access to resources
- Centralized physical resource management
- Elastic services and dynamic scaling
- Shared infrastructure
- Preconfigured VMs
- Metered services
    
    ![Screenshot 2025-03-05 140748.png](CS5224%20Cloud%20Computing%201a072aee799e80999bc3dd42f7d5e144/Screenshot_2025-03-05_140748.png)
    

### PaaS

- All in one: same IDE to develop, test, deploy, host
- Web access to development platforms
- Offline access for developers
- Scalability
    
    ![image.png](CS5224%20Cloud%20Computing%201a072aee799e80999bc3dd42f7d5e144/image%2018.png)
    

### SaaS

- Multi-tenanted applications
- Web access
- Centralized management of SaaS services
- Scalability under varying loads
- High availability
- API integration with other software
    
    ![Screenshot 2025-03-05 140819.png](CS5224%20Cloud%20Computing%201a072aee799e80999bc3dd42f7d5e144/Screenshot_2025-03-05_140819.png)
    

## Example: Setting up a Blog

### SaaS (Easiest)

- App is located on the cloud
- Web browser to access
- Built on top of multi-tiered PaaS & IaaS
1. Simply access a server with Wordpress installed and configured
2. Set up a new blog

### PaaS (More customization)

- Consumer:
    - Creates software using tools & libraries from PaaS provider
    - Controls software deployment & configuration
- Provider:
    - Installs & maintains software, libraries & middleware
    - API for consumers
    - Provides integrated services of scalability, maintenance & versioning
1. Access a system with Linux, Apache, MySQL, and PHP
2. Install and configure Wordpress blog engine
3. Set up a new blog

### IaaS (Most complex)

- Consumers need to set everything up
1. Create a compute instance running Linux
2. Install and configure Apache, MySQL, and PHP
3. Install and configure Wordpress blog engine
4. Set up a new blog

![image.png](CS5224%20Cloud%20Computing%201a072aee799e80999bc3dd42f7d5e144/image%2019.png)

## Function as a Service (FaaS)

- scalable, reactive, event-driven applications
- Functions as unit of deployment
    - application in divided into smaller, single-purpose (Lambda) functions
- event triggers a lambda function
- **no idle capacity**
- lambda is **stateless**
- build applications without the need to provision or manage servers
    - only pays when code is executing
    - no VMs
    - vendor provides provision-free **scalability**
- functions should perform only one action (small)
- functions should be self-contained (should not call other functions)
- use as few libraries as possible (for faster execution)
- e.g. app backends, chatbot, real-time data processing

### FaaS vs. PaaS vs. others

- provision time is much **lower**
- none on-going administration
- inherently **scaled**
- no capacity planning needed (built in, provider will do)
- **stateless**
- FaaS provider manages all maintenance
- high availability (functions running on provider’s servers)
- resource utilization is excellent
- has resource limits (keep function small/ lightweight)

### Lambda Function

![image.png](CS5224%20Cloud%20Computing%201a072aee799e80999bc3dd42f7d5e144/image%2020.png)

- Lambda function: app logic
- Steps:
    1. Create lambda service
        1. logic, configuration, event
    2. Deploy service created
    3. Invoke/ test your service

## MapReduce

- supports arbitrarily divisible workload
    - distributed computing on large data sets on multiple machines (100 GB, TB, PB)
- SPMD (Same program multiple data), a master instance partitions the data and gathers the partial results
    - **Split** data into blocks and assign each block to an instance/ process for parallel execution
        - intermediate output <key, value> pairs
    - **Merge** partial results produced by individual instances after all instances have completed execution (aggregate)
- Example: Word counting
    - Map: data is partitioned into 4 smaller data files ⇒ split 0 to split 3
    - Shuffle:
        - each smaller data split is evaluated by a map task
        - a map produces data (partition) or intermediate results that belongs to different reduce tasks
    - Merge and sort: partitions on arriving at a reduce task are merged in to one file and sorted to produce a sorted file
    - Reduce: evaluates the sorted file to produce the final result
- Map workers run in parallel
    - each has its own dataset
    - each creates intermediate local values from each input dataset
- Reduce workers run in parallel
    - may reduce intermediate ata

![image.png](CS5224%20Cloud%20Computing%201a072aee799e80999bc3dd42f7d5e144/image%2021.png)

![image.png](CS5224%20Cloud%20Computing%201a072aee799e80999bc3dd42f7d5e144/image%2022.png)

![image.png](CS5224%20Cloud%20Computing%201a072aee799e80999bc3dd42f7d5e144/image%2023.png)

### Hadoop

- Google MapReduce: closed source
- Hadoop MapReduce: open source
    - Hadoop Distributed File System (HDFS)
    - HDFS mimics Google File System (GFS)
    
    ![image.png](CS5224%20Cloud%20Computing%201a072aee799e80999bc3dd42f7d5e144/image%2024.png)
    

# L7 Example Applications

## K-Means Clusters

- Algorithm to cluster $m$ objects based on $n$ attributes into $k$ groups, $k<m$

![image.png](CS5224%20Cloud%20Computing%201a072aee799e80999bc3dd42f7d5e144/image%2025.png)

Steps:

1. Initialize
2. Iterate (Can work in parallel)
    1. compute distance and form clusters
    2. recompute centroids
3. Output

Map phase

- Input Key-Value pair (pre-processing)
    - Key: ID of a user
    - Value: vector of attributed of that user (e.g. followers, followees)
    - log to deal with outliers (smooth the result)
        
        ![image.png](CS5224%20Cloud%20Computing%201a072aee799e80999bc3dd42f7d5e144/image%2026.png)
        
- Compute **distance** between input point and all the previously obtained centroid of clusters
- Find the cluster with the **minimum distance** (map point to cluster)
- Add the input point into the cluster with the minimum distance
- Intermediate output: <clusters, data points belonging to that cluster> pairs

Reduce phase

- receive all the points belonging to a particular cluster
- Compute new centroid
- Output Key-Value pair
    - Key: ID of a cluster
    - Value: vector of centroid of the cluster and the number of points inside the cluster (or list of points)
        
        ![image.png](CS5224%20Cloud%20Computing%201a072aee799e80999bc3dd42f7d5e144/image%2027.png)
        

### K-Means Implementation

- Storage (AWS S3)
- MapReduce Engine (AWS EMR)

## Video-sharing SaaS Cloud Application

- User can upload, stream, download videos
- Challenges:
    - Videos are uploaded in different formats, so need to convert them into streaming format ⇒ CPU intensive
    - Upload of videos and streaming videos to multiple users ⇒ I/O intensive

### Upload Process

- Uses a web browser
- Need **interface** (web server EC2), **storage** (S3), **balancing** (load balancer, auto scaling, message passing, database, etc.)

![image.png](CS5224%20Cloud%20Computing%201a072aee799e80999bc3dd42f7d5e144/image%2028.png)

![image.png](CS5224%20Cloud%20Computing%201a072aee799e80999bc3dd42f7d5e144/image%2029.png)

### Encoding Process

- Need **video encoder** (ffmpeg, an open-source project), **server** to manage the encoding (EC2), **storage** to store encoded videos (S3), **balancing**

![image.png](CS5224%20Cloud%20Computing%201a072aee799e80999bc3dd42f7d5e144/image%2030.png)

![image.png](CS5224%20Cloud%20Computing%201a072aee799e80999bc3dd42f7d5e144/image%2031.png)

### Streaming Process

- Uses a web browser
- Need **video streamer** (Amazon CloudFront Streamer, a web service that speed up content distribution of static and dynamic web content), **storage** for encoded videos (S3), **balancing**

![image.png](CS5224%20Cloud%20Computing%201a072aee799e80999bc3dd42f7d5e144/image%2032.png)

![image.png](CS5224%20Cloud%20Computing%201a072aee799e80999bc3dd42f7d5e144/image%2033.png)

### Performance & Scaling

![image.png](CS5224%20Cloud%20Computing%201a072aee799e80999bc3dd42f7d5e144/image%2034.png)

### Pricing

- cloud vs. on-premise
- different cloud providers
- different offerings from the same provider (different EC2 instances…)

![image.png](CS5224%20Cloud%20Computing%201a072aee799e80999bc3dd42f7d5e144/image%2035.png)

# L8 Cloud Service Development

- SaaS: change the way software is **delivered**
    - Usage-based billing, high scalability, ease of access, automated updates
    - Challenges: Usability, availability, scalability, multi-tenancy, auto billing, security…
- PaaS: change the way SaaS software is **developed**
    - Automates the process of deployment, testing and scaling: all on the same platform

## SaaS vs. Traditional Software

- Pay-per-use
- Zero infrastructure: customers need not install the software
    - less security issue (installing virus)
- Reduce business cost: One-to-many, some SaaS applications shared by multiple customers
- Automated updates: SaaS updates performed by provider
- SaaS is suitable when
    - Customer require on-demand software
    - Small company
    - App with unpredictable and dynamic load (auto-scaling)
- SaaS is not suitable when
    - Real-Time processing (don’t need to upload data)
    - Data is confidential and data localization is needed
- SaaS key challenges
    - Choosing correct multi-tenancy levels (multiple people accessing the same thing)
    - Governance and security over user data (how to protect data)

## Multi-tenancy levels

- Tradeoffs: **Resource utilization/ multi-tenancy ↔ data governance**
- Self-managed: they are not managed in the cloud but locally, either by organizations or individual
1. **SaaS with self-managed infrastructure & self-managed platform** (low multi-tenancy, high data governance)
    1. Multi-tenancy: SaaS layer only
    2. Provider has full control over infrastructure and platform → user data is more controlled
    3. Cost & overhead of maintaining platform and infrastructure → lower resource utilization
2. **SaaS with self-managed infrastructure**
    1. Deployed on on-premise infrastructure
    2. Full governance over user data
    3. E.g. community deployment model
3. **SaaS with self-managed platform**
    1. Used when does not require more data governance
    2. Higher infrastructure resource utilization
    3. Overheads of maintaining platform
4. **SaaS with cloud-enabled IaaS & PaaS** (high multi-tenancy, low data governance)
    1. Shared everything
    2. High SaaS scalability through dynamic scaling by IaaS and PaaS providers
    3. High availability: backup & recovery by providers
    4. Little to no governance over user data → cross tenant attacks across all layers (DDOS)
- PaaS provides a platform to mitigate the issue of traditional software development: use a platform for testing, deployment, scaling and maintenance

## Cloud-Aware Software Development Using PaaS

- Requirement analysis (who’s the user, how/why)
- Multitenant architecture
- Dynamic scaling & availability
- Database design: database-level multi-tenancy
- SaaS development using PaaS
- Monitoring and SLA maintenance

### Deployment model

- Public PaaS: low maintenance overhead but higher security threats
- Private PaaS: high maintenance but moderate security threats
- **Higher level of multi-tenancy → higher resource utilization, lower security of data**

![Screenshot 2025-03-26 at 11.45.33 AM.png](CS5224%20Cloud%20Computing%201a072aee799e80999bc3dd42f7d5e144/Screenshot_2025-03-26_at_11.45.33_AM.png)

## Dynamic Scaling

maintain same performance on unpredictable load

- **Software load balancers**: user requests are distributed across different application and database servers
- **Hardware load balancers**: loads are distributed across different virtual machines when there is a need for more computing power
- High availability: multiple replica copies of VM and DB
- Recovery time: replica should be near customer location

## Database Design

Diverse data types of SaaS applications → use a NoSQL database to achieve scalability and availability at the database level

- Options:
    - Sharing the database instance (所有租戶共用同一個資料庫實例，包括儲存空間、資源等，可能在不同的資料表中儲存資料)
    - Sharing the database table （所有租戶共用同一個資料表，可能透過 tenant_id 欄位來做區分）
    - Sharing the database schema （每個租戶有自己的資料表或資料庫 schema，但結構相同）

## SaaS Development Using PaaS

SaaS developer/provider: PaaS tools allow developer to develop the SaaS app online and deploy on provider’s infrastructure

SaaS consumer: access SaaS using web UI provided by SaaS provider

Things to consider

- UI design to support multiple kinds of devices
- **Identify users and tenants**: role based access control
- Performance monitoring tools
- Managing users
- Self-service sign-up for users
- Usage statistics & bill calculation
- Monitoring SLA maintenance
    - Service-level agreement
    - Monitoring: tenant behavior, failure, security attacks, SLA violations
    - Updates: should not affect SaaS service, frequent automated updates to provide consumers with latest version

# L9 Pricing Models and TCO

- TCO: Total Cost of Ownership（擁有一個datacenter要花多少錢）
- move to the cloud to save $

## Pricing

- Provider: How do providers price cloud resources
- Consumers: What is the cost of using cloud resources

## Pricing Models

- Price: **unit cost** of using cloud resources
    - e.g. based on time, number of transaction
    - charge by per hour, minutes…
- Factors（考量因素）: overhead in design, development, deployment and operation of cloud services and other IT resource
    - Can reduce expenses via IT resource sharing (multi-tenancy) and optimization
    - Market competition and regulatory requirements
- Variables
    - cost metrics: on-demand (pay-per-use) or reserved allocation (upfront fee + discounted hourly rate)
    - IaaS: pricing based on resource allocation and usage
    - PaaS pricing based on software configurations, tools and licensing fees
    - SaaS: pricing based on number of application modules in the subscription, cloud service consumers, transactions
- SaaS pricing models
    - **Integrated** pricing: (common)
        - consumer pays SaaS provider
        - SaaS provider pays IaaS provider
    - **Separate** pricing:
        - consumer pays SaaS provider for SaaS service
        - consumer pays IaaS provider for hosting SaaS service
        - **complicated** for customers
    - Example: AWS pricing
        - Charges differ across region
        - On-demand: based on usage of time
            - for flexible demands (spark)
            - e.g. shopping app, concert app
        - Reserved: onetime upfront cost + pay as you go (lower than on-demand)
            - for access overtime (constant workload)
            - 需要預付費用 (long-term)
            - e.g. internal system, corporate official website
        - Spot: bid for unused capacity at a lower price than on-demand
            - for jobs not time critical, lower cost
            - e.g. machine learning jobs, data batch processing
        - Dedicated: instance runs in a VPC on hardware dedicated to single customer
            - physically isolated at the host hardware level
            - data protection is important
- Cost metrics
    - compare on-premise (TCO) vs. cloud-based provisioning
    - Business costs = capital expenses + operational expenses
        - **Up-front** costs 一開始就要支付的＄
            - capital expenses (Capex)
            - initial fund IT resources
            - on-premise is high (hardware, software, deployment costs)
            - cloud-based is low (labour cost for setting up cloud environment)
        - **On-going** costs
            - operational expenses (Opex)
            - run and maintain IT resources (license fee, electricity, maintenance)
            - on-premise is high
        - Addition costs
            - cost of capital: cost incurred to raise funds
                - 公司為了籌集資金所必須付出的代價。簡單來說，就是公司為了拿到錢（不論是從股東、銀行、還是投資人那裡），需要**付出多少代價（利息、股息、報酬期望）**。
                - if high, should go to cloud-based
            - Sunk costs: prior investment on existing IT resources
                - if high, cloud-based less appealing
                - already own/ invested $ in local infrastructure
            - Integration costs: effort needed to inter-operate IT resources on new environment
                - 把現有的 IT 系統、資料庫、應用程式等，**搬到新環境（像雲端）時，所需要花費的時間、人力、技術與金錢成本**。
                - if high, cloud-based less appealing
            - Locked-in costs: movement from one cloud provider to another
                - **從一個雲端供應商（例如 AWS）轉移到另一個供應商（例如 GCP 或 Azure）所需付出的代價。**
                - if high, cloud-based less appealing
    - Cloud usage cost metrics
        - Network usage (data transfer): inbound, outbound, intra-cloud network traffic
            - Applicable to IaaS, PaaS, SaaS
            - Inter-cloud: data replication, sync （跨雲的流量）
            - Intra-cloud: some providers may not charge for it（雲內部的流量）
        - Server usage (compute): CPU, RAM, dedicated storage…
            - On-demand: par-per-usage fee **short-term**
            - Reserved: upfront cost for reservation, **long-term**
        - Cloud storage device (storage)
            - On-demand storage space: size of storage space
            - I/O data transferred metric
        - Cloud service (service): subscription duration, # users, # transactions
    - Total cost of ownership of a datacenter (TCO)
        - Costs: capital expenses (Capex) + operational expenses (Opex)
        - Capex: upfront investment
            - construction cost of datacenter, purchase price of servers
        - Opex: recurring cost of running datacenter
            - electricity costs, repairs, maintenance, salaries
        - TCO = datacenter depreciation （折舊、成本分攤）+ datacenter Opex + server depreciation + server Opex
            - depreciation: 資產隨著時間使用、老化、貶值而產生的成本分攤
        - Understand the needs
        - e.g.
            - Case A: high-end servers (Capex heavy)
                
                ![Screenshot 2025-04-02 at 2.22.43 PM.png](CS5224%20Cloud%20Computing%201a072aee799e80999bc3dd42f7d5e144/Screenshot_2025-04-02_at_2.22.43_PM.png)
                
            - Case B: Lower-cost, high-power servers (Opex more)
                
                ![Screenshot 2025-04-02 at 2.23.00 PM.png](CS5224%20Cloud%20Computing%201a072aee799e80999bc3dd42f7d5e144/Screenshot_2025-04-02_at_2.23.00_PM.png)
                
            - Case C: Case B with 50% datacenter utilization (low utilization rate)
                
                ![Screenshot 2025-04-02 at 2.23.25 PM.png](CS5224%20Cloud%20Computing%201a072aee799e80999bc3dd42f7d5e144/Screenshot_2025-04-02_at_2.23.25_PM.png)
                
            - Summary
                
                ![Screenshot 2025-04-07 at 10.52.21 AM.png](CS5224%20Cloud%20Computing%201a072aee799e80999bc3dd42f7d5e144/Screenshot_2025-04-07_at_10.52.21_AM.png)
                

# L10

Cloud Native: how we can redesign the application to take advantage of the cloud

- containers, microservices, CI/CD, DevOps …etc

Microservice

- independently deployable services modeled around a business domain
- small & lightweight
- communicate with one another via APIs
- faster startup time, independent deployability/scalability
- loose coupling, high cohesion（凝聚力）
- Issue:
    - synchronization of data
    - scalability
    - where do we host them? ⇒ container

Container

- to host microservices
- packages up code and all its dependencies
- standardized unit for development, deployment and distribution

Docker

- open source containerization platform
- build once run anywhere

Kubernetes

- container orchestration platform