### **1. Data Collection Layer**

- **SNR Measurement**: Nodes measure the Signal-to-Noise Ratio (SNR) for each received packet. This provides an indication of signal strength and link quality.
- **Directional Feedback**: Nodes equipped with directional antennas include information about their current orientation in packets they transmit. This could be a vector or a qualitative metric (e.g., "High Directionality, Low Spread").
- **Node Metrics**: Nodes maintain lightweight, periodically refreshed metrics:
    - SNR from neighbouring nodes.
    - Hop count history for received packets.
    - Packet success rate (from acknowledgements).

---

### **2. Decision Layer**

The hop limit for each message is calculated dynamically using the following steps:

1. **Initial Hop Assignment**:
    - Base hop limit set by default (e.g., 3 hops for most scenarios).
    - If the originating node has directional capabilities, the hop limit is initially increased slightly (e.g., +1 hop) due to its longer range but reduced coverage area.
2. **Dynamic Adjustment Rules**:
    - **Low SNR Adjustment**:
        - If a node receives a message with an SNR below a threshold (e.g., 5 dB), it increases the hop limit for retransmission (+1 hop). This allows packets to potentially reach farther nodes.
    - **High SNR Suppression**:
        - If a node receives a message with a high SNR (>15 dB), it decreases the hop limit (-1 hop). This prevents overloading nearby nodes.
    - **Directional Node Boost**:
        - If a node receives a message from a known directional node and SNR is medium (e.g., 8–12 dB), the hop limit remains unchanged or gets a slight boost, depending on terrain complexity.
    - **Traffic and Congestion Awareness**:
        - Nodes monitor the frequency of retransmissions (e.g., messages/sec). If network traffic is high, hop limit adjustments are scaled down to reduce congestion.
3. **Geographic Redundancy Mitigation**:
    - Use node density to modify hop limits:
        - In dense regions, nodes suppress retransmissions for high-SNR packets (i.e., nodes assume others nearby will cover retransmission).
        - In sparse regions, nodes increase retransmission probabilities.

---

### **3. Flood Management**

Flooding is controlled using a **managed priority queue**:

- Each node maintains a queue of messages for retransmission.
- Messages with the highest adjusted hop limits are prioritised.
- Nodes periodically prune messages that have expired (based on hop limit).

---

### **Algorithm Outline**

1. **Packet Reception:**
    - Node receives a packet with metadata:
        - SNR value.
        - Hop count.
        - Sender antenna type (directional or omnidirectional).
    - Updates its local table with the sender’s metrics.
2. **Hop Limit Adjustment:**
    - Evaluate rules:
        - `if SNR < Threshold_Low: HopLimit++`
        - `if SNR > Threshold_High: HopLimit--`
        - `if Sender_Type == Directional and SNR in Medium_Range: HopLimit += Directional_Boost`
    - Calculate adjusted hop limit (`AdjustedHopLimit = Max(BaseHopLimit + Adjustments, 1)`).
3. **Queue Management:**
    - Place the packet in the retransmission queue with the adjusted hop limit.
    - Prioritise packets with higher hop limits (to ensure farther propagation first).
    - Drop expired packets (hop limit = 0).
4. **Packet Transmission:**
    - When a node retransmits a packet:
        - Reduce the hop limit by 1.
        - Include updated hop limit in metadata.
5. **Periodic Maintenance:**
    - Nodes broadcast their metrics periodically to maintain a global understanding of:
        - Nearby node density.
        - Node capabilities (directional/omnidirectional antennas).

---

## Goals, Testing, and Evaluation

Successful packet delivery likelihood is proportional to packet hop limit, whereas packet efficiency and usefulness (likelihood of received packets being packets we’ve not already seen and retransmitted) is inversely proportional to packet hop limit. So this strategy must be evaluated and compared with these two principles in mind. A successful smart hop limit adjustment implementation should be more likely to deliver packets successfully to distant or hard to reach nodes, while also not adversely impacting or improving packet usefulness.

Testing will require us to set up meshes of different topologies and compare them on these metrics between the standard fixed hop limit rules and our implemented algorithm. Testing would be a quicker and easier if we could attenuate the signals produced by the antennas to simulate worse conditions without needing a very large amount of space for the mesh. This could be implemented in the meshtastic simulator first where it is easier to see and capture all of the packets.