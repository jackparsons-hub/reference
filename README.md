# reference
Refernece

https://freecomputerbooks.com/Foundations-of-Large-Language-Models.html 

https://github.com/rasbt/LLMs-from-scratch 

https://web.stanford.edu/~jurafsky/slp3/slides/LLM24aug.pdf 

live book :
https://www.manning.com/books/knowledge-graphs-and-llms-in-action  

Scroll down to the bottom to view these appendices:


Appendices 

Read in liveBook

Appendix A: Introduction to graphs

A.1 What is a graph?

A.2 Graphs as models of networks

A.3 Representing graphs

Appendix B: Neo4j

Appendix C: Building knowledge graphs from structured sources


Appendix D: References




#  ---------------------------------------------------------------



What are the layers of OSI and how do they relate to modern cloud technology?

The OSI model breaks network communication into seven layers, each handling a specific piece of getting data from one place to another. 

Here's the rundown, bottom to top:

Layer 1 — Physical: The actual hardware: cables, radio signals, voltage, fiber optics. It's just bits moving as electrical or light signals.

Layer 2 — Data Link handles node-to-node transfer on the same local network. MAC addresses live here, along with switches and frames.

Layer 3 — Network Routing across networks. This is where IP addresses and routers operate — how a packet finds its way from your machine to a server across the internet.

Layer 4 — Transport: End-to-end delivery guarantees. TCP (reliable, ordered) and UDP (fast, no guarantees) live here. Ports (like 443 or 5432) are a Layer 4 concept.

Layer 5 — Session manages the connection/session state between two systems — establishing, maintaining, and tearing down conversations.

Layer 6 — Presentation: data translation, encryption, compression. TLS encryption and formats like JPEG or serialization formats often get discussed here.

Layer 7 — Application: The layer closest to the user. HTTP, DNS, gRPC, SMTP — the protocols applications actually speak.

How does this maps to cloud tech
Cloud computing didn't replace the OSI model; it just abstracted a lot of the lower layers away from you, while adding new complexity at the upper ones:
 
    • Layers 1–2 (Physical/Data Link): Fully abstracted in the cloud. You never touch a cable or a switch — AWS, Azure, GCP manage the physical data center fabric entirely. Your only visibility here is availability zones and regions, which map loosely to physical separation.
  
    • Layer 3 (Network): This is where cloud VPCs (Virtual Private Clouds) live conceptually. Subnets, route tables, peering, and CIDR blocks are all Layer 3 constructs, just software-defined instead of hardware-defined. This is "software-defined networking" (SDN) — cloud providers virtualize what used to be physical routers.
   
    • Layer 4 (Transport): Load balancers often operate here (Network Load Balancers in AWS terms) — routing based on TCP/UDP characteristics without inspecting the actual content.
   
    • Layer 5 (Session): Less directly visible, but persistent connections in things like database connection pooling (relevant to your Snowflake/Databricks work) or WebSocket sessions touch this layer.
   
    • Layer 6 (Presentation): TLS termination at load balancers, and increasingly, data serialization formats used across cloud services — Parquet, Avro, Protobuf — which matter a lot in a Databricks/Snowflake pipeline context, since compression and encoding choices there directly affect cost and performance.


#-------------------------------------------------------------------------------------

Data Warehousing solutions have become essential for firms trying to maximize the value of their data. Cloud-based data warehousing technology is needed for its scalability and performance.  One of the key elements that contribute to its strength is its ability to scale both up and down, allowing organizations to effortlessly adjust to changing data demands. 

#---------------------------------------------------------------------------------------


What are the advantages of Object storage? 

Compare and contrast what was used before object storage and what is used now.

Object storage is one of those shifts that quietly reshaped how cloud data platforms (including the Snowflake/Databricks stack you work with) are architected underneath. Let's compare it to what came before.

What came before: Block and File storage

Block storage (SAN — Storage Area Network)
 
    • Data is chopped into fixed-size blocks, each with a raw address, no inherent metadata 

    • The OS/filesystem sits on top and decides how blocks map to files 

    • Extremely fast, low-latency — this is what databases (including Vertica, traditionally) run on, because you need random-access reads/writes at the block level 

    • Doesn't scale horizontally in any elegant way — you scale by adding more disks/LUNs, and coordinating consistency across nodes gets hard fast 

File storage (NAS — Network Attached Storage, or local filesystems)
   
    • Data organized in a hierarchical tree: directories → subdirectories → files 
  
    • Access via file paths (/data/2026/07/sales.csv), using protocols like NFS or SMB 
   
    • Each file has some metadata (permissions, timestamps), but it's limited and fixed by the filesystem spec 
  
    • Scales reasonably for terabytes, but hits real trouble at petabyte scale — directory listing operations, metadata lookups, and lock contention all degrade as file counts explode 

Object storage (S3, Azure Blob, GCS)

Data is stored as discrete objects — each one bundling the actual data, a globally unique identifier (key), and rich, extensible metadata — inside a flat address space with no real folder hierarchy (folders are a UI illusion built from key prefixes).

The advantages, concretely

1. Virtually unlimited horizontal scalability. No hierarchical tree to maintain means no directory-listing bottleneck. Object stores are designed to scale to exabytes across thousands of nodes without the metadata overhead that cripples file systems at scale — which is exactly why every cloud data lake (and Snowflake's own storage layer, which sits on top of S3/Azure Blob/GCS) is built this way rather than on traditional file/block storage.

2. Cost Object storage is dramatically cheaper per GB than block storage — often 5–10x less — because it's designed for durability and availability over low-latency random access. This is the entire economic basis of the "separate storage from compute" architecture that Snowflake and Databricks are both built on: cheap object storage holds the data at rest, and compute clusters spin up only when needed.

3. Durability via built-in replication/erasure coding: S3-style storage typically guarantees 11 nines of durability, replicating objects across multiple availability zones automatically. Traditional file/block storage requires you to bolt on RAID, backup jobs, and replication separately.

4. Rich, extensible metadata. Each object can carry arbitrary key-value metadata (content type, custom tags, versioning info). This is what makes object stores the natural backing layer for data lakes — Parquet/Avro files with embedded schema metadata, partition tags, catalog references — versus a plain file system where metadata is limited to what the OS spec allows.

5. Native HTTP/REST access: Objects are addressed by simple GET/PUT over HTTP, not a mounted filesystem or SAN protocol. This is why it plugs so naturally into distributed compute (Spark, Databricks reading directly from S3 paths) — every node just makes stateless HTTP calls rather than needing a mounted, shared filesystem.

6. Built-in versioning and lifecycle policies: Automatic object versioning and lifecycle rules that shift cold data to cheaper storage tiers (S3 Glacier, Azure Archive) automatically — a native feature, not something you build.
Where the old approaches still win

Object storage isn't a strict upgrade — it trades away exactly what block storage is good at:

    • Latency: Object storage typically has higher latency per request (tens to hundreds of ms) versus block storage's sub-millisecond access — which is why your Vertica/OLTP-style workloads still want block storage underneath, not object storage 
   
    • No true random-write / in-place edit: You generally replace whole objects, not patch a few bytes in the middle — a real constraint for transactional workloads 
   
    • No native file-locking or POSIX semantics: multiple writers can't coordinate the way a shared filesystem allows, which matters for some legacy applications expecting POSIX behavior 
    
The one-line summary

Block storage optimizes for speed at the byte level (databases, OLTP); file storage optimizes for human-navigable organization (shared drives, legacy apps); object storage optimizes for massive scale, durability, and cost at the expense of latency and in-place editing — which is precisely why it became the default substrate for data lakes, cloud-native warehouses, and anything built around the compute/storage separation pattern that Snowflake and Databricks both rely on.

