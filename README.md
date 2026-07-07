# reference
Refernece

https://freecomputerbooks.com/Foundations-of-Large-Language-Models.html 
https://github.com/rasbt/LLMs-from-scratch 
https://web.stanford.edu/~jurafsky/slp3/slides/LLM24aug.pdf 

live book :
https://www.manning.com/books/knowledge-graphs-and-llms-in-action  
scroll down to the bottom to view these appendixes :
Appendixes 
Read in liveBook 
Appendix A: Introduction to graphs
A.1 What is a graph?
A.2 Graphs as models of networks
A.3 Representing graphs
Read in liveBook 
Appendix B: Neo4j
Read in liveBook 
Appendix C: Building knowledge graphs from structured sources
Read in liveBook 
Appendix D: references



#  ---------------------------------------------------------------



What are the layers of OSI and how do they relate to modern cloud technology ?

The OSI model breaks network communication into seven layers, each handling a specific piece of getting data from one place to another. Here's the rundown, bottom to top:
Layer 1 — Physical The actual hardware: cables, radio signals, voltage, fiber optics. It's just bits moving as electrical or light signals.
Layer 2 — Data Link Handles node-to-node transfer on the same local network. MAC addresses live here, along with switches and frames.
Layer 3 — Network Routing across networks. This is where IP addresses and routers operate — how a packet finds its way from your machine to a server across the internet.
Layer 4 — Transport End-to-end delivery guarantees. TCP (reliable, ordered) and UDP (fast, no guarantees) live here. Ports (like 443 or 5432) are a Layer 4 concept.
Layer 5 — Session Manages the connection/session state between two systems — establishing, maintaining, and tearing down conversations.
Layer 6 — Presentation Data translation, encryption, compression. TLS encryption and formats like JPEG or serialization formats often get discussed here.
Layer 7 — Application The layer closest to the user. HTTP, DNS, gRPC, SMTP — the protocols applications actually speak.

How this maps to cloud tech
Cloud computing didn't replace the OSI model, it just abstracted a lot of the lower layers away from you, while adding new complexity at the upper ones:
    • Layers 1–2 (Physical/Data Link): Fully abstracted in the cloud. You never touch a cable or a switch — AWS, Azure, GCP manage the physical data center fabric entirely. Your only visibility here is availability zones and regions, which map loosely to physical separation.
    • Layer 3 (Network): This is where cloud VPCs (Virtual Private Clouds) live conceptually. Subnets, route tables, peering, and CIDR blocks are all Layer 3 constructs, just software-defined instead of hardware-defined. This is "software-defined networking" (SDN) — cloud providers virtualize what used to be physical routers.
    • Layer 4 (Transport): Load balancers often operate here (Network Load Balancers in AWS terms) — routing based on TCP/UDP characteristics without inspecting the actual content.
    • Layer 5 (Session): Less directly visible, but persistent connections in things like database connection pooling (relevant to your Snowflake/Databricks work) or WebSocket sessions touch this layer.
    • Layer 6 (Presentation): TLS termination at load balancers, and increasingly, data serialization formats used across cloud services — Parquet, Avro, Protobuf — which matter a lot in a Databricks/Snowflake pipeline context, since compression and encoding choices there directly affect cost and performance.
