### **Node State Classification**

Each node in the network maintains a dynamic state based on its energy levels and role. Key states include:

- **High-Energy Node (Active Router)**: Nodes with sufficient power route traffic aggressively.
- **Low-Energy Node (Minimal Router)**: Nodes in power-saving mode route traffic selectively.
- **Critical Node (Passive Listener)**: Nodes with very low energy participate minimally in the network, primarily for receiving messages.

**Trigger Mechanism**:

- A node entering a low-energy or critical state broadcasts a message like `LOW_BATTERY` or `MINIMAL_ROUTING` to neighbouring nodes.
- This informs other nodes to deprioritise the low-energy node for relaying traffic.

---

### 2. **Flooding Modulation**

Modify the managed flooding algorithm to consider the node state:

- **Normal Nodes**: Use default managed flooding behaviour (e.g., rebroadcast all messages within a certain hop limit).
- **Low-Energy Nodes**:
    - Reduce the likelihood of rebroadcasting (e.g., introduce a probabilistic delay or suppression timer based on energy level).
    - Prioritise personal or directed messages over general traffic.
    - Evaluate packet metadata to avoid redundant retransmissions.
- **High-Energy Nodes**:
    - Actively increase their rebroadcast likelihood to compensate for low-energy nodes.
    - Extend their hop limit slightly to ensure coverage.

---

### 3. **Duty-Cycle Adaptation**

Nodes with low battery can dynamically reduce their activity to conserve energy:

1. **Wake-Sleep Scheduling**:
    - Implement an adaptive duty cycle where nodes sleep for longer intervals and wake periodically to check for traffic.
    - Adjust the sleep time based on the remaining battery level (e.g., shorter sleep intervals when energy is higher).

---

## Goals, Testing, and Evaluation

We need some way of testing power consumption, if the nodes aren’t able to self-report accurate or granular enough power consumption metrics. Plugs that can measure power draw?

Testing will be done in the same way as most other techniques we implement with routing algorithms - implement it in the simulator first and then create a physical mesh and test against the current implementation.

Main metrics to consider are the effect on packet deliverability as nodes that sleep for longer intervals may miss some packets if they wake up too late to receive. One thing we haven’t considered here is if we could increase the preamble length for the transmission to allow for even longer sleep and lower power consumption, at the cost of negligible overhead as the preamble is small (currently 8 pulses) compared to the subsequent packet length (max 255 bytes), but I am not sure if that is something we could even change. Packet round-trip time might also be a consideration however I do not know if packets received are timestamped at a receiving node so it might not be something that we can accurately measure or the effect might be negligable if a critical pathway between two nodes does not contain a sufficient amount of low-power nodes. So success would be measured by whether we are able to reduce power consumption by allowing nodes to enter a low-power state for longer, and to reduce their active participation in the mesh, without heavily impacting the reliability of the mesh.

### References:

- [https://www.geeksforgeeks.org/wireless-sensor-network-wsn/](https://www.geeksforgeeks.org/wireless-sensor-network-wsn/)
- [https://ieeexplore.ieee.org/document/6483049](https://ieeexplore.ieee.org/document/6483049) (changing the duty cycle for it).