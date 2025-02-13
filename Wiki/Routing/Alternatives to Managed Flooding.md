These are simple algorithms, so should be easy to implement.

1. **Probabilistic Flooding (Gossip-Based Routing):**

Instead of forwarding every received message, nodes forward messages with a certain probability (p). This reduces redundancy while maintaining good reachability.

Pros:

- Reduces message duplication compared to standard flooding.
- Still simple, doesn’t require route maintenance.
- Works well in large, dynamic networks.

Cons:

- Some messages might not reach all nodes if probability p is too low.
- No guaranteed delivery.

Optimisations for Meshtastic:

- Adaptive probability: Nodes could adjust p based on network density (it uses a similar mechanism to the density-based idea).
- Selective retransmission: Nodes track if a message is “rare” (not heard much) and increase p for those (the node has no history of it so its probably the first one to hear it / the only one so make sure it retransmits it).

Based on:

[https://arxiv.org/pdf/cs/0209011](https://arxiv.org/pdf/cs/0209011) (suggests p in rage of 0.65-0.75)

1. **Geographic-Based Flooding (Geo-Routing)**

How It Works:

Nodes only forward packets towards the destination, rather than blindly rebroadcasting.

Pros:

- Great for long-range networks like LoRa because it avoids unnecessary transmissions.
- Reduces congestion in denser parts of the network.
- Works well if nodes know their approximate location.

Cons:

- Requires GPS or estimated position, which consumes power. (not sure if all nodes have GPS or even if we can demand that from the nodes).
- Harder to implement in non-geographic applications.

Optimisations for Meshtastic:

- Region-based flooding: Only nodes in a specific region rebroadcast.
- Directional forwarding: Nodes closer to the target location are more likely to forward (kind of like the directionality algorithm idea).

Based on:

[https://www.cs.cornell.edu/projects/Quicksilver/public_pdfs/stefan_mobihoc.pdf](https://www.cs.cornell.edu/projects/Quicksilver/public_pdfs/stefan_mobihoc.pdf)

1. Gradient-Based Routing (Hop Count or Signal Strength) (similar to Hop count adjuster)

How It Works:

Nodes forward messages only if they are "closer" to the target based on a gradient metric (e.g., hop count, signal strength).

Pros:

- Avoids unnecessary retransmissions.
- Uses simple metrics that can work on LoRa.
- Can be optimised to balance reliability vs. efficiency.

Cons:

- Requires nodes to track metrics like hop count or signal strength.
- Might fail if signal strength fluctuates too much.

Optimisations for Meshtastic:

- Hop-based cutoff: Drop messages after a set number of hops.
- Signal-strength biasing: Nodes with stronger signals rebroadcast more often.

Based on:

[https://rtcl.eecs.umich.edu/papers/thesis/jjinlim_thesis.pdf](https://rtcl.eecs.umich.edu/papers/thesis/jjinlim_thesis.pdf)

1. Controlled Flooding (Self-Pruning)

How It Works:

Nodes rebroadcast only if they think it will improve delivery, based on:

- Whether they are a critical bridge in the network (if the node itself sees a small number of nearby nodes).
- If they’ve seen few copies of the message.
- If they have good link quality to other nodes (received ACK from nodes promptly).

Pros:

- Reduces redundancy and congestion.
- Keeps flooding behaviour, but makes it smarter.
- Works without precomputed routes.

Cons:

- Requires each node to keep some state about recent messages.
- Needs good heuristics to decide when to forward.

Optimisations for Meshtastic:

- Rare-message priority: If a message was heard only once, rebroadcast with high probability. (similar to 1.)
- Node degree awareness: Nodes with fewer neighbours rebroadcast more (have to be aware of surroundings).

Based on:

[https://www.microsoft.com/en-us/research/wp-content/uploads/2009/04/msr-tr-2009-37.pdf](https://www.microsoft.com/en-us/research/wp-content/uploads/2009/04/msr-tr-2009-37.pdf)

Same as 1.

1. Epidemic Routing with Buffering

How It Works:

Nodes store messages and opportunistically forward them when they meet a new node.

Pros:

- Works well for intermittent connectivity. (should help with frequently connecting and disconnecting nodes)
- Ensures messages eventually propagate in low-density networks.

Cons:

- Messages can linger too long if not managed properly.
- Higher memory usage (not ideal for small LoRa devices, so the number of messages each node stores has to be dynamic and removes oldest messages that don’t fit anymore to keep newer ones available) (I wonder how many packets can be stored on the device).

Optimisations for Meshtastic:

- TTL (Time-to-Live): Messages expire after a set time.
- Priority queues: Important messages get forwarded first.

Based on:

[http://issg.cs.duke.edu/epidemic/epidemic.pdf](http://issg.cs.duke.edu/epidemic/epidemic.pdf)

**Cool paper that talks about managed flooding a little bit:**

[https://www.researchgate.net/publication/332213663_Evaluation_of_a_LoRa_mesh_wireless_networking_system_supporting_time-critical_transmission_and_data_lost_recovery_poster_abstract](https://www.researchgate.net/publication/332213663_Evaluation_of_a_LoRa_mesh_wireless_networking_system_supporting_time-critical_transmission_and_data_lost_recovery_poster_abstract)

**Cool paper talks about meshtastic:**

[https://www.researchgate.net/publication/380253012_Exploring_open_source_and_proprietary_LoRa_mesh_technologies](https://www.researchgate.net/publication/380253012_Exploring_open_source_and_proprietary_LoRa_mesh_technologies)