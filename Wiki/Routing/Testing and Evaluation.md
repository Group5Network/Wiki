

For testing and evaluation, a multi-tiered approach is ideal that combines both physical tests with 20 nodes and extensive simulations to model larger network scales. Here’s an outline of the strategy:

Note that these numbers are just examples so we are free to adjust them. 

1. Simulated Network Scale:
    - Range of Nodes: Simulate networks starting with a single node and incrementally increasing up to 1,000 nodes.
    - Number of Realisations: For statistical significance, generate approximately 1,000 example networks at each node-count level. This will help us understand the performance and reliability trends as the network scales.
2. Key Metrics and Dimensions:
    - Primary Metrics:
        - Packet Delivery Ratio (PDR)
        - Latency
    - Secondary Metrics:
        - Packet overhead
        - Extra computation (e.g. queue lengths, retransmission counts)
        - Throughput
    - Reliability Conditions:
        - Vary network reliability by simulating scenarios where a percentage of nodes (ranging from 0% up to a point where no messages get through) disconnect or fail intermittently. This adds a third dimension to our data.
    - Gateway Density:
        - Evaluate the impact of increasing the number of gateways. Simulate scenarios from a single gateway up to a gateway per node, to identify the minimum number of gateways required to achieve maximal reliability and acceptable overhead.
3. Comparison Table and Analysis:
    
    - Algorithm Thresholds: Create a comparison table indicating at which network sizes and under what conditions each routing algorithm outperforms the default managed flooding approach. For example, note the node count where overhead becomes negligible relative to gains in reliability.
    - Cost vs. Performance Trade-off: Include analysis on how increasing the number of gateways affects overall performance, helping to determine the minimal deployment needed for optimal performance.


|**Number of Nodes**|**Algorithm**|**PDR (%)**|**Latency (ms)**|**Packet Overhead (%)**|**Throughput (kbps)**|**Network Reliability (%)**|**Number of Gateways**|
|---|---|---|---|---|---|---|---|
|10|Flooding|98|200|25|50|100%|1|
|10|Algorithm 1|99|150|15|60|90%|1|
|50|Flooding|90|400|30|45|85%|1|
|50|Algorithm 2|93|350|20|55|80%|2|
|100|Flooding|80|600|35|40|75%|2|
|100|Algorithm 1|85|500|28|50|70%|3|
|500|Flooding|60|1000|50|20|50%|5|
|500|Algorithm 2|70|900|40|30|40%|6|
Something like this but some things kept the same, so it would be 4D overall and loads of data generated :) . 


Can also do combination of algorithms as a stretch. 



And for the video, can do the same experiment but in the peak district (of course will less nodes and testing, but just a couple) :) on a hypothetical network. Find the limitations of these improvements.


Procedurally Generated Tests:
![[IMG_6121.jpg]]
Do we want to avoid situations where there is max 1 hop? 
Do we prefer max distance tests (from furthest node to furthest gateway)
Or just loads of random tests? What amount of tests would be good enough so that we can disregard the random one hop tests? 
If we were to eliminate 1 hops scenarios, then in big networks 2 hops might have the same effect so dynamically calculating if in this scenario hops required are proportional to the size of the network? but that depends on topology, so might have to do a network calculation. 

Can we assume only one node is communicating? Meshtastic is known for infrequent communication and in this case, it would be use for emergencies only so can we just do that? Or do we add another dimension to rests to how many packets each node is communicating? But that communication density shouldnt be uniform as some nodes should transmit more than others. Hmmmm. 

