**Managed-flooding assessment:**

Meshtastic uses Managed Flooding as its primary routing mechanism. This is a simple but robust approach where messages are broadcasted to all nearby nodes, and each node selectively rebroadcasts based on predefined rules (e.g., only forwarding if it's likely to improve delivery).

**Pros:**

- Simple and doesn’t require route maintenance.
- Works well in sparse networks where paths are unreliable.
- Good for intermittent connectivity.

**Cons:**

- Can lead to unnecessary message duplication.
- Increased congestion in dense networks.
- No guaranteed delivery (best-effort basis).

We don’t have access to the papers.

**Original papers of this concept:**

[https://www.sciencedirect.com/science/article/abs/pii/0169755286900255?via%3Dihub](https://www.sciencedirect.com/science/article/abs/pii/0169755286900255?via%3Dihub)

Work in progress of getting it.

[https://dl.acm.org/doi/10.1145/1013812.18196](https://dl.acm.org/doi/10.1145/1013812.18196)

[https://resilinets.org/flooding_and_epidemic_routing.html](https://resilinets.org/flooding_and_epidemic_routing.html)

**Article of Meshtastic as to why managed flooding:**

[https://meshtastic.org/blog/why-meshtastic-uses-managed-flood-routing/](https://meshtastic.org/blog/why-meshtastic-uses-managed-flood-routing/)

**How to update the routing algorithms:**

[https://meshtastic.org/docs/development/firmware/build/](https://meshtastic.org/docs/development/firmware/build/)