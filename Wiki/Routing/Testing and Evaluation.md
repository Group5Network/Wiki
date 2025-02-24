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

| **Number of Nodes** | **Algorithm** | **PDR (%)** | **Latency (ms)** | **Packet Overhead (%)** | **Throughput (kbps)** | **Network Reliability (%)** | **Number of Gateways** |
| ------------------- | ------------- | ----------- | ---------------- | ----------------------- | --------------------- | --------------------------- | ---------------------- |
| 10                  | Flooding      | 98          | 200              | 25                      | 50                    | 100%                        | 1                      |
| 10                  | Algorithm 1   | 99          | 150              | 15                      | 60                    | 90%                         | 1                      |
| 50                  | Flooding      | 90          | 400              | 30                      | 45                    | 85%                         | 1                      |
| 50                  | Algorithm 2   | 93          | 350              | 20                      | 55                    | 80%                         | 2                      |
| 100                 | Flooding      | 80          | 600              | 35                      | 40                    | 75%                         | 2                      |
| 100                 | Algorithm 1   | 85          | 500              | 28                      | 50                    | 70%                         | 3                      |
| 500                 | Flooding      | 60          | 1000             | 50                      | 20                    | 50%                         | 5                      |
| 500                 | Algorithm 2   | 70          | 900              | 40                      | 30                    | 40%                         | 6                      |

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

Leandro Questions:
- dummy packets in testing
- verify simulator, aggregated results are enough?
- rcf. email, copy leandro (cc), 

Dummy packets. 

Note about nodes going on and off:
Rather than saying flat out if a node procedurally generated is on or off completely all the time,
make it rather that a node goes on and off randomly.
Simple idea like any node can go offline (except sender and destination node maybe?) at any time for a random amount of time.
Modelled mathematically it can be something like:
at each timestep in a simulator, a node has a 1% chance of going offline.
in the next timestep, it has a 1% chance of going back online but that % increases the longer it has been offline for, so that it is more likely to come back online the longer it has been offline for.
This can be modelled as a simple exponential decay function.

$P_{\text{on}}(n) = 1 - e^{-\lambda n}$
At each timestep ($t$), a node has a probability $P_{\text{off}} = 0.01$ of going offline.  
If offline for ($n$) timesteps, the probability of coming back online follows an exponential decay model:

$$P_{\text{on}}(n) = 1 - e^{-\lambda n}$$

where $(\lambda)$ controls the rate at which the node returns online.

The probability of a node going offline at any timestep or if a node is already offline, whats the rate of it coming back online, all are variables that can be changed.
So we can test how the routing changes in both variables vary.

Another Note:
NodeInfo broadcasts information to other nodes that it exists every 3 hours.
We can decrease this so nodes know more often if a node goes back into its range, so it can
send it the buffer of message that new node has probably not seen. However, we dont want to clutter the network with too many messages, so maybe if a node could identify itself as a mobile and add nearby nodes more often to send it packets it might have not seen based on the timestep. So not every node sends its info every 3 hours, but only the mobile nodes do.
Hmmmm.

Should we do a different retransmission policy? 

How many retransmission would increase the probability of receiving the message at the destination?
Variate that for another dimension of data.

But can also do a different algorithm for that, like time of day etc. 