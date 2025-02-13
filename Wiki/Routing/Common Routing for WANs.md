(I believe they are mostly used for broadband so not really relevant…)

**Comparison: (the algorithms are better described in the papers)**

DSDV (Destination-Sequenced Distance Vector) and OLSR (Optimised Link State Routing) are **proactive** routing protocols that maintain up-to-date routing tables by periodically exchanging information. DSDV ensures low-latency for established routes and prevents loops, but its high control overhead makes it inefficient for low-bandwidth networks like LoRa. OLSR optimises link-state updates to reduce overhead, making it suitable for medium-to-dense networks, but it still demands too much processing power and frequent control packets, making it impractical for Meshtastic.

AODV (Ad hoc On-Demand Distance Vector) and HWMP (Hybrid Wireless Mesh Protocol) follow different approaches, with AODV being reactive, creating routes only when needed, and HWMP being a hybrid protocol that combines proactive tree-based routing with on-demand paths. AODV reduces unnecessary control messages, making it more efficient in dynamic networks, though initial route discovery delays and memory usage could be concerns. It could be viable for LoRa if optimised. HWMP, while adaptable to changing topologies, is primarily designed for Wi-Fi mesh networks and introduces unnecessary complexity, making it unsuitable for Meshtastic.

BATMAN and Babel offer more adaptive routing approaches. BATMAN is proactive, dynamically forming routes based on link-quality broadcasts, making it decentralised and self-adjusting. However, its reliance on frequent message exchanges could be problematic in large networks. If modified for LoRa, it may be a viable option. Babel, a **hybrid** protocol combining distance-vector methods with link-quality estimation, is well-suited for dynamic networks but is too complex and control-heavy for low-power LoRa applications.

**Meta-analysis of these:**

[https://www.diva-portal.org/smash/get/diva2%3A831024/FULLTEXT01.pdf](https://www.diva-portal.org/smash/get/diva2%3A831024/FULLTEXT01.pdf)

Meta analysis of DSDV, AODV, OLSR and HWMP.

[https://www.mdpi.com/2673-4001/5/4/51](https://www.mdpi.com/2673-4001/5/4/51)

Meta analysis of OLSR, BATMAN, and Babel

[](https://www.researchgate.net/publication/381889712_EVALUATING_ROUTING_ALGORITHMS_ACROSS_DIFFERENT_WIRELESS_MESH_NETWORK_TOPOLOGIES_USING_NS-3_SIMULATOR?utm_source=chatgpt.com)[https://www.researchgate.net/publication/381889712_EVALUATING_ROUTING_ALGORITHMS_ACROSS_DIFFERENT_WIRELESS_MESH_NETWORK_TOPOLOGIES_USING_NS-3_SIMULATOR](https://www.researchgate.net/publication/381889712_EVALUATING_ROUTING_ALGORITHMS_ACROSS_DIFFERENT_WIRELESS_MESH_NETWORK_TOPOLOGIES_USING_NS-3_SIMULATOR)

Meta analysis of AODV, DSDV, and OLSR

[https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=7573902](https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=7573902)

Cool analysis of many of them.

**Problems for applications in Meshtastic:**

LoRa networks have very low data rates (typically 300 bps – 50 kbps). Proactive protocols like OLSR and DSDV send frequent updates, which would consume too much bandwidth.

Routing tables require memory and CPU cycles, which drain battery faster. To implement AODV and BATMAN you would need to reduce control overhead, which is too big of a task.

So I think none of them will work :(