
**What Is a Redundant Path?**

A redundant path is an alternative route a packet can take from source to destination after the first successful delivery. If multiple nodes rebroadcast a message, the packet can arrive at the destination via different intermediate nodes.

In a flooding-based system like Meshtastic:

- A high number of redundant paths suggests robustness (higher reliability).
- Too many redundant paths can create congestion (increasing interference and latency).

**How to Calculate Redundant Paths?**

Step 1: Log Packet Transmission Paths

Each Meshtastic node could log:

- Packet ID (Unique identifier for each message)
- Sender Node ID (Who sent it)
- Next-Hop Nodes (Which neighbours rebroadcasted it)
- Final Destination ID (Where it arrived)
- Timestamps (For time-based analysis)

This telemetry data helps reconstruct the network graph over time.

Step 2: Reconstruct the Network Graph

Using logged data, construct a directed acyclic graph (DAG) where:

- Each node represents a Meshtastic device.
- Each edge represents a rebroadcast event.
- Each path from source to destination is a possible route.

Step 3: Count Unique Paths Per Message

Using graph traversal algorithms (like Breadth-First Search or Dijkstra’s Algorithm for weighted cases):

- Identify all paths from the source to the destination.
- Filter paths that successfully delivered the message.
- Count how many independent paths exist after the first successful one.

**Example Metric: Redundancy Factor (RF):**
$$RF = \frac{\text{Total successful paths to destination}}{\text{Minimum number of hops needed}}$$

- If RF > 1, multiple redundant paths exist.
- If RF ≈ 1, minimal redundancy exists.


**How This Helps Meshtastic?**

- If a message reaches its destination via multiple paths, the network is resilient.
- If there’s too much redundancy, managed flooding settings might need to be adjusted.
- If redundancy is low, a better rebroadcasting strategy is needed to avoid message loss.

