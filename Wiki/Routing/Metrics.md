**Cool metric for reliability:**
https://arxiv.org/abs/1103.5957?%5D(https://arxiv.org/abs/1103.5957)

The **Flooding Path Probability (FPP)** metric measures the reliability of packet delivery in flooding-based routing. It calculates the end-to-end probability that a packet reaches its destination, assuming each node only rebroadcasts after hearing from all upstream neighbours. This ensures redundancy is captured in the model, making it particularly suited for broadcast applications such as emergency alerts and network-wide synchronisation. By explicitly modelling packet forwarding rather than relying on heuristics, FPP provides a mathematically grounded assessment of flooding reliability.

The **Unicast Retransmission Flow (URF)** metric evaluates reliability in unicast routing by considering the probability of successful packet delivery. It assumes a relay node retransmits a packet on its outgoing links until it either receives an acknowledgement or exhausts all options. This model closely reflects practical retransmission mechanisms in wireless networks, making it ideal for point-to-point communication such as VoIP or data streaming. The explicit formulation of URF enables a more precise understanding of reliability compared to conventional heuristic-based approaches.

A key advantage of these metrics is their foundation in explicit probability models rather than estimation-based heuristics. This makes them more accurate in assessing real-world network conditions.


**Testing criteria:**

Robustness to Node Failures: Nodes may fail or go offline, requiring dynamic adjustments to the routing table.

Scalability: As the network grows, routing algorithms might struggle with increased complexity and traffic.

Energy Efficiency: Routing algorithms must balance path optimisation with energy consumption, especially for battery-operated nodes.

Load Balancing: Avoiding over-reliance on a few central nodes to prevent premature failure and maintain network performance.

Latency Sensitivity: In time-sensitive applications, routing algorithms need to minimise latency without sacrificing reliability.

Environmental Factors: Weather, terrain, and physical obstacles can significantly affect LoRa’s performance, requiring adaptive algorithms.

Total number of packets being sent, number of acknowledgments being sent. Can you add stochastic noise. Channel modes. Fading channel.

[https://arxiv.org/pdf/cs/0209011](https://arxiv.org/pdf/cs/0209011)

Is there a difference between reliability and likelihood.

**Reliability:**

Packet delivery ratios and packet overhead

Extra computation, latency, etc.

- packet generation rate -> how many retransmissions has it done/ how many are in the queue. how busy is the node.