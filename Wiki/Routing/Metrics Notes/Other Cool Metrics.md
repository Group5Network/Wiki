Note that I don't believe they can be used but leaving them here for later.

**Cool metric for reliability:**
https://arxiv.org/abs/1103.5957?%5D(https://arxiv.org/abs/1103.5957)

(these )
The **Flooding Path Probability (FPP)** metric measures the reliability of packet delivery in flooding-based routing. It calculates the end-to-end probability that a packet reaches its destination, assuming each node only rebroadcasts after hearing from all upstream neighbours. This ensures redundancy is captured in the model, making it particularly suited for broadcast applications such as emergency alerts and network-wide synchronisation. By explicitly modelling packet forwarding rather than relying on heuristics, FPP provides a mathematically grounded assessment of flooding reliability.

The **Unicast Retransmission Flow (URF)** metric evaluates reliability in unicast routing by considering the probability of successful packet delivery. It assumes a relay node retransmits a packet on its outgoing links until it either receives an acknowledgement or exhausts all options. This model closely reflects practical retransmission mechanisms in wireless networks, making it ideal for point-to-point communication such as VoIP or data streaming. The explicit formulation of URF enables a more precise understanding of reliability compared to conventional heuristic-based approaches.

A key advantage of these metrics is their foundation in explicit probability models rather than estimation-based heuristics. This makes them more accurate in assessing real-world network conditions.
