**Trigger Mechanism:**

- A node triggers a **load-balancing check** when:
    - It detects that its queue is full or nearly full (e.g., based on buffer occupancy or retries).
    - It receives multiple acknowledgements indicating congestion (e.g., delayed or dropped packets from neighbours).
    - The density of nearby nodes exceeds a threshold (detected via HELLO packets or regular telemetry).
- Trigger propagation:
    - If the load issue persists after local adjustments, the node sends a broadcast alert to neighbours, initiating their re-evaluation of routes.

**Local Node Clustering:**

- Nodes form **localised clusters** in dense regions based on proximity (measured via RSSI/SNR).
- Within each cluster:
    - A **coordinator node** is elected (temporarily) based on factors like remaining energy, processing capacity.
    - The coordinator handles inter-cluster communication and ensures minimal rebroadcasting within the cluster.

### References

- [](https://datatracker.ietf.org/doc/draft-ietf-manet-cbrp-spec/#:~:text=Cluster%20Based%20Routing%20Protocol%20(CBRP)%20is%20a%20routing%20protocol%20designed,clusters%20in%20a%20distributed%20manner)[https://datatracker.ietf.org/doc/draft-ietf-manet-cbrp-spec/#:~:text=Cluster](https://datatracker.ietf.org/doc/draft-ietf-manet-cbrp-spec/#:~:text=Cluster) Based Routing Protocol (CBRP) is a routing protocol designed,clusters in a distributed manner.
- [https://ieeexplore.ieee.org/abstract/document/1493749](https://ieeexplore.ieee.org/abstract/document/1493749)
- [](https://en.wikipedia.org/wiki/Low-energy_adaptive_clustering_hierarchy#:~:text=4%20References-,Protocol,cluster%20head%20in%20this%20round)[https://en.wikipedia.org/wiki/Low-energy_adaptive_clustering_hierarchy#:~:text=4](https://en.wikipedia.org/wiki/Low-energy_adaptive_clustering_hierarchy#:~:text=4) References-,Protocol,cluster head in this round.