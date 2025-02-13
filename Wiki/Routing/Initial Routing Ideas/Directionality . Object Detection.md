### Key Strategies:

1. **Signal Strength and Directionality Detection**
    
    Nodes equipped with directional antennas can gather data about signal directionality. This information can be used to prioritise routes or optimise retransmissions.
    
    - **RSSI/SNR Analysis**:Nodes measure the Received Signal Strength Indicator (RSSI) and Signal-to-Noise Ratio (SNR) of incoming packets. High RSSI values indicate a stronger signal, while SNR helps estimate the quality of the signal.
    - **Antenna Orientation Feedback**:Directional nodes can include their orientation (e.g., a simple angle in degrees) in their packets. Neighbouring nodes can use this data to estimate which directions offer better connectivity.
2. **Neighbour Awareness and Directional Tables**
    
    Each node maintains a local "neighbour table" with information about nearby nodes, including:
    
    - Node ID
    - Signal strength (RSSI/SNR)
    - Directional feedback (if available)
    - Obstacle data (if available from telemetry or inferred from weak signals in certain directions).
3. **Dynamic Routing Adjustment**
    
    Nodes dynamically adjust their flooding behaviour based on directionality and signal strength:
    
    - **Directional Rebroadcasting**:Nodes with directional antennas send retransmissions preferentially in the direction of the strongest signal or highest neighbour density.
    - **Prioritised Flooding**:Packets are forwarded first to nodes in the preferred direction (based on signal data) and later rebroadcast to other directions.
4. **Obstacle Detection and Avoidance**
    
    Obstacles create blind spots that reduce network performance. These can be handled using:
    
    - **Environmental Scanning**: Nodes equipped with telemetry sensors (e.g., GPS or accelerometers) can detect movement or infer obstacles based on dropped packets or consistently weak signals.
    - **Feedback Mechanism**: Nodes encountering obstacles broadcast a "low signal feedback" message to help neighbouring nodes adjust their directional transmissions or rebroadcast strategies.

### Algorithm Design:

1. **Initialisation**
    
    - Nodes equipped with directional antennas share their orientation (e.g., `orientation = 45°`).
    - Each node measures RSSI/SNR for incoming packets and logs this data into its neighbour table.
2. **Packet Reception**
    
    When a node receives a packet, it follows this process:
    
    - Update the neighbour table with RSSI, SNR, and directional metadata (if present).
    - Evaluate signal strength trends to detect possible obstacles (e.g., if RSSI suddenly drops from a specific direction).
3. **Flooding Decision**
    
    For retransmission:
    
    - **Directional Priority**: Select neighbours with higher RSSI/SNR values and rebroadcast to those directions first.
    - **Signal Gradient Evaluation**: Use the RSSI/SNR trend to favour rebroadcasting towards nodes likely to propagate the message further (higher signal strength gradients).
    - **Obstacle Avoidance**: Avoid rebroadcasting to nodes with consistent signal drop-offs or flagged as obstacle-prone.
4. **Periodic Re-Evaluation**
    
    - Nodes periodically update their neighbour table and recalculate optimal transmission directions based on new signal data and directional feedback.

Is it too much computation?

### References:

[https://www.mdpi.com/2079-9292/11/13/2032](https://www.mdpi.com/2079-9292/11/13/2032)
