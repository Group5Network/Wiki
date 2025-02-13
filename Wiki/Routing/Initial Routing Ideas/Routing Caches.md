
### **1. Cache Initialisation and Management**

- **Cache Structure**:
    - Each node maintains a local **routing cache table** in memory, indexed by `Destination Node ID`.
    - Each entry contains:
        - `Destination Node ID`
        - `Next Hop Node ID`
        - `Route Expiry Timestamp`
        - `Reliability Score` (optional: based on past success metrics)
    - Optionally, limit cache size to a predefined value (`MAX_CACHE_ENTRIES`) to prevent memory overuse.
- **Cache Lifespan**:
    - Use **time-to-live (TTL)** for entries. Expired entries are purged to ensure stale routes don't persist in dynamic networks.
    - For networks with frequent topology changes, consider adaptive TTL based on observed stability of nodes.

---

### **2. Route Discovery with Managed Flooding**

- On receiving a packet:
    1. Check the **cache** for a valid entry (`Destination Node ID` matches).
    2. If a cached route exists:
        - Forward the packet to the `Next Hop Node` without rebroadcasting to all neighbours.
    3. If no cache entry exists:
        - Use **managed flooding** to propagate the packet as usual.
        - Nodes receiving the flood add the **last node in the route** as the `Next Hop` in their local cache.

---

### **3. Updating the Cache**

- **On Successful Delivery**:
    - If an ACK or delivery confirmation is received:
        - Update the cache's `Reliability Score` for the route.
        - Optionally, extend the TTL for the route if delivery time was optimal.
- **On Route Failure**:
    - If no ACK is received or delivery fails:
        - Decrease the `Reliability Score` for the cached route.
        - If the score drops below a threshold, mark the route as invalid and remove it from the cache.

---

### **4. Periodic Validation of Cached Routes**

- Use occasional **probe packets** (low-priority packets) to validate cached routes:
    - Send a probe through the `Next Hop Node`.
    - If the probe fails, the route is invalidated.
    - This ensures that cache entries remain valid without relying solely on flooding.

---

### **5. Memory-Efficient Cache Design**

- Employ **least-recently-used (LRU)** or **frequency-based eviction** to manage limited cache space:
    - LRU: Replace the least recently used entry when adding a new route.
    - Frequency-based: Prioritise keeping routes to highly accessed nodes.

---

### **Data Flow Example**

1. **Packet Origination**:
    
    - Node A sends a message to Node F. Node A checks its cache and finds no entry for Node F.
2. **Managed Flooding**:
    
    - Node A floods the packet, including its own ID in the route header.
    - Intermediate nodes (e.g., B, C, D) receive the packet. They cache the `Next Hop` as:
        - Node B → Next Hop = A
        - Node C → Next Hop = B
        - Node D → Next Hop = C
3. **Route Delivery**:
    
    - When Node F receives the packet, it sends an ACK back through the cached route.
4. **Subsequent Packet**:
    
    - If Node A sends another packet to Node F:
        - Node A checks the cache and forwards the packet directly to Node B, reducing the need for flooding.
5. **Telemetry Integration**:
    
    - Use telemetry data (RSSI, SNR, battery level) to inform cache entry reliability and adjust TTL dynamically.
6. **Flooding Overrides**:
    
    - Ensure cached routes don't entirely override flooding in case of failures. A hybrid model retains flooding for rare or dynamic cases.
7. **Clustered Caching**:
    
    - In dense networks, cache entries can include cluster-level routing (i.e., designate a nearby node as a gateway for high-density regions).
8. **Broadcast Handling**:
    
    - For broadcast messages (to all nodes), bypass the cache to prevent suppression of essential updates.

---

### **Metrics to Evaluate**

- **Packet Delivery Ratio (PDR)**:
    - Compare with and without caching to measure improvements.
- **Average Latency**:
    - Measure time savings for cached vs. flooded routes.
- **Overhead Reduction**:
    - Quantify the decrease in rebroadcasts and redundant transmissions.
- **Memory Usage**:
    - Monitor memory utilisation to ensure scalability.

### References

- [](https://en.wikipedia.org/wiki/Dynamic_Source_Routing#:~:text=Dynamic%20Source%20Routing%20(DSR)%20is,table%20at%20each%20intermediate%20device)[https://en.wikipedia.org/wiki/Dynamic_Source_Routing#:~:text=Dynamic](https://en.wikipedia.org/wiki/Dynamic_Source_Routing#:~:text=Dynamic) Source Routing (DSR) is,table at each intermediate device.
-