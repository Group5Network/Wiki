**Managed-flooding assessment:**

Meshtastic uses Managed Flooding as its primary routing mechanism. This is a simple but robust approach where messages are broadcasted to all nearby nodes, and each node selectively rebroadcasts based on predefined rules (e.g., only forwarding if it's likely to improve delivery).

**Pros:**

- Simple and doesn’t require route maintenance.
- Works well in sparse networks where paths are unreliable.
- Good for intermittent connectivity.

**Cons:**

- Can lead to unnecessary message duplication.
- Increased congestion in dense networks.
- No guaranteed delivery (best-effort basis).


**How Managed-flooding works:**
Meshtastic routing uses a simple, multi-layered approach inspired by the RadioHead mesh routing protocol. Here's a high-level breakdown of its routing mechanism:

1. Layer 0 - LoRa Radio: The device converts data into LoRa symbols for transmission, with a preamble used to synchronise clocks and a physical header containing packet length and sync word (set to 0x2B for Meshtastic).
2. Layer 1 - Unreliable Zero Hop Messaging: This layer sends messages without reliability guarantees. Each packet includes headers with destination, sender, and packet IDs, as well as a flag for acknowledgment (if required). The protocol employs CSMA/CA (Carrier-Sense Multiple Access with Collision Avoidance) to detect channel activity and reduce the chances of packet collisions.
3. Layer 2 - Reliable Zero Hop Messaging: For improved reliability, this layer adds acknowledgment (ACK) requests. If a node does not receive an acknowledgment within a certain time, it will attempt to resend the message up to three times. Broadcast messages utilise flooding and implicit ACKs, ensuring broad message propagation without channel congestion.
4. Layer 3 - Managed Flooding for Multi-Hop Messaging: This is the core of Meshtastic’s routing strategy. Nodes rebroadcast messages until a predefined hop limit is reached. Before rebroadcasting, nodes listen to see if another node has already done so, preventing redundant retransmissions. The system uses dynamic contention windows based on the received signal strength (SNR), so distant nodes with lower SNRs are more likely to transmit first, while closer nodes defer transmission. Certain nodes, like routers and repeaters, have higher priority to rebroadcast.

Checkout the docs for more details. 


We don’t have access to the papers.

**Original papers of this concept:**

[https://www.sciencedirect.com/science/article/abs/pii/0169755286900255?via%3Dihub](https://www.sciencedirect.com/science/article/abs/pii/0169755286900255?via%3Dihub)

Work in progress of getting it.

[https://dl.acm.org/doi/10.1145/1013812.18196](https://dl.acm.org/doi/10.1145/1013812.18196)

[https://resilinets.org/flooding_and_epidemic_routing.html](https://resilinets.org/flooding_and_epidemic_routing.html)

**Article of Meshtastic as to why managed flooding:**

[https://meshtastic.org/blog/why-meshtastic-uses-managed-flood-routing/](https://meshtastic.org/blog/why-meshtastic-uses-managed-flood-routing/)

**How to update the routing algorithms:**

[https://meshtastic.org/docs/development/firmware/build/](https://meshtastic.org/docs/development/firmware/build/)