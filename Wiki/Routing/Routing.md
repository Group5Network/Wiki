**Reliability-Enhancing Routing Changes from Initial Ideas:**
Smart Hop Limit Adjustment, Density-Based Routing, and Directionality Algorithms are expected to improve reliability. The Smart Hop Limit Adjustment allows messages to propagate further when the signal-to-noise ratio is low (i.e., when the source is farther away), ensuring messages reach greater distances. Density-Based Routing addresses situations where an excessive number of packets cause queues to overflow, leading to message loss. In dense environments, this approach should enhance reliability, as nodes that might otherwise be ignored in the original algorithm are now more likely to process messages. The impact of Directionality Algorithms on reliability remains uncertain.

Check out the other initial routing ideas in the `Initial Routing Ideas` folder.

From `Alternatives to Managed flooding`, the most promising ones are:
1. Probabilistic Flooding
2. Controlled Flooding
3. Epidemic Routing with Buffering



These algorithms are interesting but I think require too much changes, as they require a lot of computation, which the typical Meshtastic nodes can’t handle but still interesting read.
`Common Routing for WANs`


Checkout `Alternatives for Managed Flooding` for the details of the algorithms mentioned below. 
Couple of good algorithms from the link above:

1. Probabilistic Flooding
2. Controlled Flooding
3. Epidemic Routing with Buffering

**Shortlist of routing algorithms in the order we should try them:**

| Order | Algorithm                       | Why                                                                                                                                                                                                                                 | Does it apply to reliability?                                                                                                                                                                  |
| ----- | ------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1     | Smart-hop Limit                 | It is simple to implement and test out if we can successfully edit the routing algorithm.                                                                                                                                           | Yes                                                                                                                                                                                            |
| 2     | Probabilistic Flooding          | It reduces congestion and is simple to implement, so it should increase reliability.                                                                                                                                                | Should but in dense environments                                                                                                                                                               |
| 3     | Epidemic Routing with Buffering | It keeps the messages in a buffer so when a new node join, it receives all the packets                                                                                                                                              | Yes, as nodes frequently connect and disconnect so we can test this.                                                                                                                           |
| 4.    | Controlled Flooding             | It only floods if the node thinks it will improve delivery chance (more complex telemetry data but still simple) so it reduces congestion very simply as well.                                                                      | Should                                                                                                                                                                                         |
| 5.    | Density-based routing           | This will be difficult algorithm to implement as this is the clustering one. But should definitely help in dense areas.                                                                                                             | In dense areas only, yes.                                                                                                                                                                      |
| 6.    | Directionality Algos            | Will require either GPS or the user telling us on a map where the node is (thats unreliable anyways as the nodes can be mobile). Will also need to figure out the direction the antenna is pointing and compare it to the GPS data. | It should find more optimal paths if the exist and prioritise nodes with antennas in the right direction, reducing congestion but it wont increase reliability too much if only in rare cases. |
| 7.    | Stationary Nodes                | More telemetry but will make the network more efficient.                                                                                                                                                                            | No                                                                                                                                                                                             |
| 8.    | Traffic-aware                   | Deals with bursts better so makes the network more robust.                                                                                                                                                                          | No, except in bursts situations.                                                                                                                                                               |
| 9.    | Energy Efficient Routing        | Improves efficiency by adapting the network to battery level of the nodes.                                                                                                                                                          | No                                                                                                                                                                                             |
| 10.   | Routing Caches                  | Improves retransmission of packets amounts (reducing it) as the packets will be addressed to specific nodes if they still exist but there are complications here so it will be hard.                                                | No                                                                                                                                                                                             |

Checkout `Metrics` for Metric details. 

Checkout `Reliability` for details on how reliability is defined.

**What do we want to test for:**
Checkout `Testing and Evaluation` for details.

**Hardware Limitations:**
Checkout `Hardware Limitations`

Success Criteria:
Checkout `Success Criteria`