Three sections:

- Simulator
- Routing
- Gateways

MVP (pass):

- Simulator
- Large scale Automated Testing (procedural generated) of routing algorithms.
- Allow for testing of routing algorithms in the simulator.
- Simulate the gateways.
- Routing
- Modifying managed flooding slightly to evaluate if it improved reliability. or implement 1 routing algorithm to test. Test it specifically for reliability (percentage chance it reaches the destination). Worse case reliability. Average reliability.
- Gateways
- Frequency conversion gateway (between the 2 bands). Channel Gateway. Bridge.

Stretch goals:

- Simulator
- Allow for the metrics mentioned below to be captured.
- Improve the simulator based on data captured in the real world.
- Rewrite the simulator.
- Routing
- Implement more routing algorithms (from 2 to the 8 we got in Notion or more if we find any)
- Evaluate them against more metrics, like time taken to transmit, hops used, Robustness to Node Failures, Scalability, how much battery/power used by nodes and how that changes depending on what the node has done, how well balanced was the load, how performance changes depending on Environmental Factors. More metrics can be added. packet loss, collision.
- Gateways
- APRS gateway
- Returns the package back to the Meshtastic Node from the gateway.

What do we want to test in the real world:

- Identify interesting cases in the simulator and try to recreate them in the real world so we can improve the simulator more…
- Peak district for Video :) :)
- Add more noise/disturbing notes/ limit range.
- How many nodes inside of the experiments.
- observability, piggy backing,
- how to find malicious nodes (which would allow for prioritisation fo messages).