
### **A. Node Identification Mechanism**

1. **Node Status Detection**:
    
    - **Flag-based identification:** During network initialisation, nodes declare their status (e.g., "stationary" or "mobile") via configuration flags or predefined settings.
    - **Telemetry-based detection:** Nodes can monitor their power source. For example:
        - Detect whether they are connected to mains power or running on battery.
        - Communicate this power state to the network via periodic status broadcasts.
2. **Stationary Node Registry**:
    
    - Maintain a distributed table or cache across the network, listing known stationary nodes with their unique IDs and reliability metrics (e.g., uptime percentage, battery level).
    - Or add that information into the top of the packets, …
    - How would a node know which nodes are battery powered? Periodic status updates to all the nearby nodes when not busy? But this adds clutter to the system.
3. During the network initialisation phase, all nodes broadcast their status.
    
4. Nodes receiving this information build a local map of node statuses (e.g., a table of `node_id: status`).
    
5. The map is shared and synchronised across the network during regular routing updates.
    
6. I feel like there are many issues with this approach…
    

### Strategies:

- **Priority Rebroadcasting**:
    - Stationary nodes rebroadcast critical messages with higher probability than mobile nodes.
    - Mobile nodes reduce rebroadcast probability when they detect nearby stationary nodes have already broadcast the message.
- **Flood Origin Adjustment**:
    - When a mobile node originates a message, it can designate a nearby stationary node as the primary relay point.

1. When a message is generated, nodes within range receive it and evaluate:
    - Is the sender stationary? If so, rebroadcast normally.
    - Is the sender mobile? Forward the message only if no stationary nodes in range are likely to handle it.
2. Use backoff timers:
    - Stationary nodes set a shorter backoff timer for rebroadcasting to take precedence.
    - Mobile nodes extend their backoff timers to let stationary nodes handle the majority of traffic.

### **3. Load-Balanced Routing**

Once stationary nodes are identified and prioritised, you can optimise how traffic flows through the network:

### Strategies:

- **Geographic Clustering**:
    - Divide the network into "zones" based on signal strength or location data.
    - Within each zone, designate a stationary node (if available) as the cluster head.
    - Cluster heads route traffic between zones, reducing redundant rebroadcasts from other nodes.
- **Traffic Allocation**:
    - Critical or high-priority messages are routed through stationary nodes.
    - Less critical traffic can be handled by mobile nodes to balance network load.

### Algorithm Flow:

1. **Initial Setup**:
    - Each stationary node calculates its "zone of influence" based on signal strength (e.g., RSSI) and broadcasts this zone to nearby nodes.
2. **Message Routing**:
    - Mobile nodes preferentially forward messages to the nearest stationary node in their zone.
    - Stationary nodes forward messages to other stationary nodes or mobile nodes as needed.
3. **Dynamic Adjustment**:
    - As nodes join or leave the network, zones are recalculated, and routing tables are updated.

---

### **4. Evaluating Impact**

Once implemented, analyse the effect of prioritising stationary nodes on network performance. Use metrics like:

- **Power Consumption**: Compare the battery drain on mobile nodes with and without prioritisation.
- **Reliability**: Measure delivery success rates for critical messages.
- **Latency**: Observe delays in message propagation under various traffic loads.

---

### **Flow Diagram Example**

1. **Initialisation**:
    - Nodes broadcast their `power_status` and signal strength.
    - Neighbours build a `node_table` containing IDs and statuses.
2. **Message Propagation**:
    - **Step 1**: Node receives a message.
    - **Step 2**: Check if a stationary node is in range:
        - **Yes**: Defer rebroadcast; rely on the stationary node.
        - **No**: Handle rebroadcast based on the managed flooding protocol.
3. **Dynamic Updates**:
    - Periodically refresh `node_table` with updated status and telemetry data.

### **Key Challenges**

- **Telemetry Overhead**: Frequent status updates can increase network traffic. Use periodic updates or piggyback on existing message exchanges.
- **Stationary Node Failure**: Include fallback mechanisms for when stationary nodes go offline. For example, allow mobile nodes to resume normal rebroadcasting in their absence.
- **Load Balancing**: Ensure traffic prioritisation doesn’t overload stationary nodes.

This idea kind of blends with the others, where in clustering we have a zone of influence too… ground for integration.

### References:

- [https://www.researchgate.net/publication/270959070_Design_of_infrastructure-based_wireless_mesh_network](https://www.researchgate.net/publication/270959070_Design_of_infrastructure-based_wireless_mesh_network)
- [https://link.springer.com/article/10.1007/s10922-009-9143-3](https://link.springer.com/article/10.1007/s10922-009-9143-3)

[https://www.researchgate.net/publication/270959070_Design_of_infrastructure-based_wireless_mesh_network](https://www.researchgate.net/publication/270959070_Design_of_infrastructure-based_wireless_mesh_network)