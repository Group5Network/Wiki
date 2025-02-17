
## **Key Performance Metrics:**

1. Packet Delivery Ratio (PDR)

Definition:  
Packet Delivery Ratio (PDR) measures the reliability of the network by calculating the percentage of successfully delivered packets compared to the total number of packets sent. A high PDR indicates an efficient and reliable network.

Calculation:$$\frac{\text{Number of packets received successfully at the destination}}{\text{Total number of packets sent by the source}} \times 100\%$$
How to Collect from Nodes:

- Each node should log every packet it generates, retransmits, and receives.
- Destination nodes should acknowledge received packets.
- Lost packets can be inferred from the difference between sent and received packets across all nodes.
- Telemetry data should include:
    - Packet ID (to track individual messages).
    - Timestamp of sending and receiving (to match sent and received packets).
    - Sender and receiver node IDs (to reconstruct routing paths).
    - Total transmissions per packet (to assess reliability per attempt).

2. Latency

Definition:  
Latency measures the time taken for a packet to travel from the source to the destination. Lower latency is ideal, especially in time-sensitive emergency situations. One packet sent only (not realistic so maybe changes in the future). 

Calculation:

$${Latency} = \text{Timestamp at destination} - \text{Timestamp at source}$$

How to Collect from Nodes:

- Each packet should be timestamped at the moment of transmission.
- Upon receipt, the final destination should compare the timestamp to its local time.
- Nodes should also log delays due to queuing, retransmissions, and processing time.
- Data to collect:
    - Send time and receive time per packet.
    - Number of hops taken (to analyse delays at each node).
    - Retransmission delays (to determine the impact of resending packets).

### **Secondary Performance Metrics**:

3. Packet Overhead

Definition:  
Packet overhead refers to the additional control and retransmission data required to ensure successful delivery. High overhead can congest the network and increase energy consumption.

Calculation:$${Overhead} = \frac{\text{Total control and retransmission packets}}{\text{Total delivered data packets}}$$
Telemetry Data to Collect:
- Number of retransmissions per packet.
- Number of control messages exchanged (e.g., routing updates).
- Packet sizes of both data and control messages.

4. Extra Computation (Node Load & Processing Time)

Definition:  
Extra computation measures how much processing power is required per node for routing decisions, retransmissions, and queue management.

How to Measure:

- CPU usage per node over time.
- Time taken to process and forward each packet.
- Number of packets in the queue at any given time.
- Logging the decision-making delay for forwarding packets.

5. Packet Generation Rate & Node Utilisation**

Definition:  
Packet generation rate refers to how frequently nodes generate new packets. Node utilisation measures how busy each node is with handling, forwarding, or queueing packets.

How to Measure:
- Count the number of new packets generated per second per node.
- Measure queue length at each node over time.
- Identify the percentage of time a node spends forwarding versus idle.

6. Throughput

Definition:  
Throughput is the rate at which data is successfully transmitted across the network. Higher throughput ensures better performance, but excessive congestion can degrade reliability.

Calculation:$${Throughput} = \frac{\text{Total data successfully received}}{\text{Time taken}}$$
How to Collect:
- Log the total size of successfully received packets per unit time.
- Differentiate between data packets and retransmitted packets.


### Other Cool Metrics

Checkout `Metrics Folder` for some cool ones we can try to implement. 

**We also want to record:**
Total number of packets being sent, number of acknowledgments being sent. 


Note that all of the above metrics are calculated for each scenario, we would take the average of it for each number of nodes in our table of results. 