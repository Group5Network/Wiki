### 1. **Traffic Monitoring and Trigger Mechanism**

- Each node monitors its **traffic load** in real time, focusing on:
    - **Packet queue size**: Indicates the level of congestion.
    - **Processing delay**: Measures how quickly packets are handled.
    - **Channel utilisation**: Tracks the time the channel is busy.
- A **trigger event** is fired if the node reaches a defined threshold, such as:
    - Queue size > 80% of capacity.
    - Processing delay exceeds a set limit.
    - A specific SNR threshold is breached, indicating reduced performance.
- **Trigger Action**: The node broadcasts a **congestion signal** to nearby nodes.

### 2. **Neighbouring Nodes’ Response to Congestion Signal**

- On receiving a congestion signal, nearby nodes perform the following steps:
    - Check their own traffic loads to determine their ability to take over routing.
    - Assess the quality of alternative paths (e.g., via SNR, hop count).
    - Use a **probabilistic approach** to decide whether to reroute or retain the current path:
        - Reuse old routes with a certain probability (e.g., 60%) to maintain stability.
        - Explore alternative routes with the remaining probability (e.g., 40%) to avoid overloading neighbours.

### 3. **Adaptive Load Balancing**

- Nodes dynamically adjust routing using these strategies:
    - **Weighted Probabilistic Forwarding**:
        - Assign weights to potential routes based on metrics like:
            - Path SNR.
            - Current traffic load of nodes on the path.
            - Hop count to the destination.
        - Use these weights to select the next hop probabilistically, balancing traffic across multiple paths.
- **Temporary Route Suppression**:
    - Mark highly congested nodes as temporarily unavailable for routing (soft suppression) for a specific duration, giving them time to recover.

### 4. **Algorithm Flow**

1. **Normal Operation**:
    - Nodes flood packets using the managed flooding approach, optimised for their local conditions.
2. **Congestion Detection**:
    - A node at capacity broadcasts a congestion signal to its neighbours.
3. **Neighbour Response**:
    - Neighbouring nodes assess their suitability to take over traffic based on their own load and path metrics.
4. **Rerouting Decision**:
    - Neighbours probabilistically adjust their forwarding rules to reroute traffic away from the congested node, balancing the load across available paths.
5. **Stabilisation**:
    - Nodes monitor traffic and adjust back to original routes if the congestion subsides, maintaining efficiency without unnecessary load balancing.

### 5. **Metrics and Feedback Loops**

- Regularly update metrics such as:
    - Node load (queue size, delay).
    - Path quality (SNR, reliability).
    - Neighbour responsiveness.

### References:

- [https://www.geeksforgeeks.org/tcp-congestion-control/](https://www.geeksforgeeks.org/tcp-congestion-control/)
- [https://www.rfc-editor.org/rfc/rfc5681?trk=public_post_comment-text](https://www.rfc-editor.org/rfc/rfc5681?trk=public_post_comment-text)
- [https://en.wikipedia.org/wiki/Software-defined_networking](https://en.wikipedia.org/wiki/Software-defined_networking)