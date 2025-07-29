
### **A. Node Identification Mechanism**

1. **Node Status Detection**:
    
    - **Flag-based identification:** During network initialisation, nodes declare their status (e.g., "stationary" or "mobile") via configuration flags or predefined settings.
    - **Telemetry-based detection:** Nodes can monitor their power source. For example:
        - Detect whether they are connected to mains power or running on battery.
        - Communicate this power state to the network via periodic status broadcasts.
2. **Stationary Node Registry**:
    
    - Maintain a distributed table or cache across the network, listing known stationary nodes with their unique IDs and reliability metrics (e.g., uptime percentage, battery level).
    - Or add that information into the top of the packets, …
    - How would a node know which nodes are battery powered? Periodic status updates to all the nearby nodes when not busy? But this adds clutter to the system.
3. During the network initialisation phase, all nodes broadcast their status.
    
4. Nodes receiving this information build a local map of node statuses (e.g., a table of `node_id: status`).
    
5. The map is shared and synchronised across the network during regular routing updates.
    
6. I feel like there are many issues with this approach…
    

### Strategies:

- **Priority Rebroadcasting**:
    - Stationary nodes rebroadcast critical messages with higher probability than mobile nodes.
    - Mobile nodes reduce rebroadcast probability when they detect nearby stationary nodes have already broadcast the message.
- **Flood Origin Adjustment**:
    - When a mobile node originates a message, it can designate a nearby stationary node as the primary relay point.

1. When a message is generated, nodes within range receive it and evaluate:
    - Is the sender stationary? If so, rebroadcast normally.
    - Is the sender mobile? Forward the message only if no stationary nodes in range are likely to handle it.
2. Use backoff timers:
    - Stationary nodes set a shorter backoff timer for rebroadcasting to take precedence.
    - Mobile nodes extend their backoff timers to let stationary nodes handle the majority of traffic.

### **3. Load-Balanced Routing**

Once stationary nodes are identified and prioritised, you can optimise how traffic flows through the network:

### Strategies:

- **Geographic Clustering**:
    - Divide the network into "zones" based on signal strength or location data.
    - Within each zone, designate a stationary node (if available) as the cluster head.
    - Cluster heads route traffic between zones, reducing redundant rebroadcasts from other nodes.
- **Traffic Allocation**:
    - Critical or high-priority messages are routed through stationary nodes.
    - Less critical traffic can be handled by mobile nodes to balance network load.

### Algorithm Flow:

1. **Initial Setup**:
    - Each stationary node calculates its "zone of influence" based on signal strength (e.g., RSSI) and broadcasts this zone to nearby nodes.
2. **Message Routing**:
    - Mobile nodes preferentially forward messages to the nearest stationary node in their zone.
    - Stationary nodes forward messages to other stationary nodes or mobile nodes as needed.
3. **Dynamic Adjustment**:
    - As nodes join or leave the network, zones are recalculated, and routing tables are updated.

---

### **4. Evaluating Impact**

Once implemented, analyse the effect of prioritising stationary nodes on network performance. Use metrics like:

- **Power Consumption**: Compare the battery drain on mobile nodes with and without prioritisation.
- **Reliability**: Measure delivery success rates for critical messages.
- **Latency**: Observe delays in message propagation under various traffic loads.

---

### **Flow Diagram Example**

1. **Initialisation**:
    - Nodes broadcast their `power_status` and signal strength.
    - Neighbours build a `node_table` containing IDs and statuses.
2. **Message Propagation**:
    - **Step 1**: Node receives a message.
    - **Step 2**: Check if a stationary node is in range:
        - **Yes**: Defer rebroadcast; rely on the stationary node.
        - **No**: Handle rebroadcast based on the managed flooding protocol.
3. **Dynamic Updates**:
    - Periodically refresh `node_table` with updated status and telemetry data.

### **Key Challenges**

- **Telemetry Overhead**: Frequent status updates can increase network traffic. Use periodic updates or piggyback on existing message exchanges.
- **Stationary Node Failure**: Include fallback mechanisms for when stationary nodes go offline. For example, allow mobile nodes to resume normal rebroadcasting in their absence.
- **Load Balancing**: Ensure traffic prioritisation doesn’t overload stationary nodes.

This idea kind of blends with the others, where in clustering we have a zone of influence too… ground for integration.

### References:

- [https://www.researchgate.net/publication/270959070_Design_of_infrastructure-based_wireless_mesh_network](https://www.researchgate.net/publication/270959070_Design_of_infrastructure-based_wireless_mesh_network)
- [https://link.springer.com/article/10.1007/s10922-009-9143-3](https://link.springer.com/article/10.1007/s10922-009-9143-3)

[https://www.researchgate.net/publication/270959070_Design_of_infrastructure-based_wireless_mesh_network](https://www.researchgate.net/publication/270959070_Design_of_infrastructure-based_wireless_mesh_network)



## 3.1 Neighbor Count-Based Probability Decay

To dynamically adjust rebroadcast probability based on local network density, several decay functions were implemented. Each function is defined below, along with its intended behavior and advantages. The decays are compared in **Figure X**.

**Exponential Decay**

$$P(n) = \begin{cases} 1 & \text{if } n \leq 2 \\ \max\left(P_{\text{min}}, e^{-\alpha n}\right) & \text{if } n > 2 \end{cases} \quad \text{with} \quad P_{\text{min}} = 0.3, \ \alpha = 0.1$$

Exponential decay was chosen to aggressively suppress redundant rebroadcasts as node density increases, while maintaining a floor probability $P_{\text{min}}$ for robustness.

**Grace Cliff Function**

$$P(n) = \begin{cases} 1 & \text{if } n \leq 10 \\ 0.3 & \text{otherwise} \end{cases}$$

This function maintains high reliability up to a defined threshold, with soft suppression beyond it to mitigate broadcast storms while preventing complete silencing.

**Harsh Cliff Function**

$$P(n) = \begin{cases} 1 & \text{if } n \leq 10 \\ 0 & \text{otherwise} \end{cases}$$

The harsh cliff provides strict cutoff behaviour, ensuring no rebroadcasting in very dense regions. It was tested to observe extremes of suppression.

**Piecewise Hybrid Decay**

$$P(n) = \begin{cases} 1 & \text{if } n \leq 2 \\ 0.7 & \text{if } n = 3 \\ \max\left(0.3, \frac{0.7}{\log n}\right) & \text{if } n > 3 \end{cases}$$

This hybrid approach combines guaranteed rebroadcasting at low densities with gradual suppression at higher densities, balancing reliability and efficiency.

**Inverse Linear Decay**

$$P(n) = \max\left(0.3, \min\left(1, \frac{k}{n}\right)\right) \quad \text{with} \quad k = 2$$

Inverse linear decay suppresses rebroadcasts smoothly with increasing neighbours, while guaranteeing high probability in sparse areas (≤2 neighbours).

**Logarithmic Decay**

$$P(n) = \begin{cases} 1 & \text{if } n \leq 2 \\ \max\left(0.3, \min\left(1, \frac{1}{\ln(n + 1)}\right)\right) & \text{if } n > 2 \end{cases}$$

Logarithmic decay decays slowly at low densities and accelerates at higher densities, offering fine-grained control particularly suited for medium-density environments.

**Sigmoid Decays**

$$P(n) = \begin{cases} 1 & \text{if } n \leq 2 \\ \frac{1}{1 + e^{k(n - n_0)}} & \text{if } n > 2 \end{cases} \quad \text{with} \quad k \in \{0.1, 0.2, 0.3\},\ n_0 = 10$$

Three sigmoid variants with different kk values were tested to evaluate sensitivity to the steepness of the probability transition. A smaller $k$ yields a gradual decrease; a larger $k$ results in a sharper cutoff around $n_0$.

---

## 3.2 SNR-Based and Combined Probability Strategies

SNR is a measurement of ... . Under Meshtastic, it is included in each packet that a node receives so it can help understand the environement without having to centralise any information or sending additional packets. 

**SNR-Based Linear Mapping**

A linear mapping was used to translate the received packet SNR (range approximately −15  $\text{dB}$ to 20 $dB$) into rebroadcast probability:

$$P(\text{SNR}) = \begin{cases} 1 & \text{if } \text{SNR} \leq -15\,\text{dB} \\ \frac{20 - \text{SNR}}{35} & \text{if } -15\,\text{dB} < \text{SNR} < 20\,\text{dB} \\ 0.3 & \text{if } \text{SNR} \geq 20\,\text{dB} \end{cases}$$

Nodes with weaker links (lower SNR) are more likely to rebroadcast, prioritising reliability.
Reasoning why lower SNR is proritised is ...

**Piecewise SNR Strategy**

An additional piecewise SNR decay was defined:

- For SNR < 0 dB: always rebroadcast.
    
- For 0 ≤ SNR < 10 dB: linear decay.
    
- For 10 ≤ SNR ≤ 20 dB: exponential decay.
    

This strategy ensures robust retransmission over weak links while efficiently suppressing rebroadcasts with strong signal quality.

**Combined Neighbor and SNR-Based Strategies**

Three combined strategies were developed:

1. **Equal weighting**: average of neighbor-based and SNR-based probabilities.
    
2. **SNR-favored weighting**: higher weight on SNR.
    
3. **Neighbor-favored weighting**: higher weight on neighbor count.
    

Combined approaches allow tuning between network density control and link quality prioritization, depending on deployment requirements.

---

Would you also like me to quickly sketch **Figure X** as a plot showing all the decays for you? (I could generate a nice clear one if you want.)  
Also, would you like a slightly even more compact version (if you’re aiming for like 1–1.5 pages max)? 🚀


Of course! Here's a full continuation of your section, following your tone and structure:

---

**SNR-Based Linear Mapping**

SNR is a measurement of signal strength relative to background noise, expressed in decibels ($dB$). A linear mapping was used to translate the received packet SNR (range −15 dB to 20 dB) into rebroadcast probability:

$$P(\text{SNR}) = \begin{cases} 1 & \text{if } \text{SNR} \leq -15\,\text{dB} \\ \frac{20 - \text{SNR}}{35} & \text{if } -15\,\text{dB} < \text{SNR} < 20\,\text{dB} \\ 0.3 & \text{if } \text{SNR} \geq 20\,\text{dB} \end{cases}$$

Nodes with weaker links (lower SNR) are more likely to rebroadcast, prioritising reliability.

Lower SNR values indicate that a transmission has been received with higher noise levels, suggesting that the communication link is fragile. By increasing the rebroadcast probability for such links, the system compensates for potential losses, ensuring that messages have a greater chance of reaching other parts of the network. High SNR values indicate a strong, clear link, where the risk of packet loss is lower so rebroadcasting becomes less critical and can be suppressed to reduce redundant traffic.

SNR-based approach provides **real-time, per-packet feedback** on link quality, capturing the effects of dynamic environmental factors like interference, obstacles, and mobility.



Yeah, definitely — **you can combine the Grace Cliff and Harsh Cliff into a _single generalised equation_** by introducing a parameter to control whether you want "soft" or "hard" suppression.

Here’s a neat way to write it:

---

$$P(n) = \begin{cases} 1 & \text{if } n \leq 10 \\ \alpha & \text{otherwise} \end{cases}

\text{where}  

 \alpha = \begin{cases} 0.3 & \text{for Grace Cliff (soft suppression)} \\ 0 & \text{for Harsh Cliff (hard suppression)} \end{cases}$$

---

**Explanation:**

- The _core logic_ is the same for both.
    
- You introduce $\alpha$ to describe what happens after 10 neighbours.
    
- $\alpha$ is 0.3 for Grace Cliff and 0 for Harsh Cliff.
    

---

If you want it **even tighter** (e.g., for saving even more space) you could write:

> A generalised cliff function was defined, with rebroadcast probability $P(n)$ depending on a parameter $\alpha$ that controls suppression strength. Grace Cliff used $\alpha=0.3$ for soft suppression beyond 10 neighbours; Harsh Cliff used $\alpha=0$ for strict suppression.

And then just put the one equation I gave you.

---

Would you like me to also quickly show a _super compact_ version where it's even just one line of text and equation together? (Useful if you're short on space.)  
(Also, if you want, I can suggest a slightly better name than "Grace Cliff" / "Harsh Cliff" that still sounds cool but a bit more formal!)

Certainly! Below is a detailed technical description of your `Agent` class implementation, going beyond the conceptual recap of Jeff Jones’ model and diving into the precise mathematical formulation and behavioral logic expressed in your code. This explanation is suitable for inclusion in documentation, a technical report, or an implementation section of a paper.

---

## 🧠 Agent Implementation Details (`agent.py`)

The `Agent` class implements a biologically inspired, particle-like agent that mimics the taxis-driven behavior of *Physarum polycephalum*. Each agent navigates a 2D environment using local chemoattractant gradients, updates its movement direction via internal sensory inputs, and interacts with an environment grid through deposition and reorientation mechanisms. The following subsections describe the mathematical and algorithmic foundations of each component.

---

### 1. Initialization: `__init__(self, width, height, x, y, SO, SA, RA, depT)`

Each agent is initialized with:

- `width`, `height`: Dimensions of the 2D environment grid.
- `x`, `y`: Initial floating-point position of the agent within the environment.
- `SO` (*Sensor Offset*): Distance (in grid cells) ahead of the agent at which sensory input is collected.
- `SA` (*Sensor Angular Aperture*): Angular half-width (in radians) of the frontal sensing cone. Total field of view = `2 × SA`.
- `RA` (*Rotation Angle*): Angular change applied to the agent’s direction per unit difference in sensed chemoattractant.
- `depT` (*Deposition Threshold*): Constant amount of chemoattractant deposited upon successful movement.

The initial direction of the agent is sampled uniformly from the full 360° range:
$$

\theta_{\text{init}} \sim \mathcal{U}(-\pi, \pi)
$$

…and stored as a unit vector:

$$

\vec{d} = (\cos(\theta_{\text{init}}), \sin(\theta_{\text{init}}))
$$

Additionally, a fixed *sensor angle* is chosen to orient the center of the agent's sensor cone:

$$\[
\phi \sim \mathcal{U}(0, 2\pi)
\]$$

This defines the forward-facing direction of sensing relative to the agent's current position.

---

### 2. Sensing: `sense(local_grid)`

At each timestep, the agent samples chemoattractant levels in its local vicinity using three angularly distributed sensors positioned at a fixed Euclidean offset `SO` from its current location `(x, y)`.

#### Sensor Directions:

For each sensor \( i \in \{0,1,2\} \), compute the angle:
$$
\[
\theta_i = \phi + \sigma_i
\]$$

where:
$$
\[
\sigma_0 = -SA, \quad \sigma_1 = 0, \quad \sigma_2 = +SA
\]
$$
The sensor positions in Cartesian grid coordinates are computed via modular arithmetic to enforce periodic boundary conditions:

$$\[
(x_i, y_i) = \left( \left(x + SO \cdot \cos(\theta_i) \right) \mod w,\ \left(y + SO \cdot \sin(\theta_i) \right) \mod h \right)
\]$$

Each sensor reads the attractant level from the trail map layer (assumed to be stored in channel index `1` of `local_grid`):

\[
c_i = \text{trail\_level}_i = \text{local\_grid}[y_i, x_i, 1]
\]

The sensor with the maximum attractant concentration is selected, and the agent’s direction is updated according to the signed difference in concentration between the middle sensor and the strongest neighboring sensor. The rotation applied to the current direction vector is proportional to this difference and scaled by parameter `RA`:

\[
\Delta \theta = RA \cdot \text{argmax}_{i}(\{c_i\})\ \text{relative to central sensor}
\]

If the strongest sensor is at index 1 (central), no rotation occurs. Otherwise, the turn direction is determined by whether the peak is left (+RA) or right (−RA) of center.

The new direction vector is recomputed as:

\[
\vec{d}_{\text{new}} = (\cos(\phi + \sigma_{\text{winner}}),\ \sin(\phi + \sigma_{\text{winner}}))
\]

This models a form of *adaptive taxis*, where the agent aligns more closely with stronger gradients of attractant.

---

### 3. Movement: `move()`

The agent attempts to move forward in the current direction, wrapping around grid edges using modulo arithmetic to simulate an infinite planar topology:

\[
x_{\text{new}} = (x + d_x) \mod w \\
y_{\text{new}} = (y + d_y) \mod h
\]

Note that physical collision detection (e.g., overlapping with other agents) is not performed in this function—movement is assumed to be non-preemptive and will fail silently if another agent has already moved to the target cell (this is handled externally during scheduling).

---

### 4. Projected Move Calculation: `project_move()`

Convenience method that returns the *planned* next position without altering the agent’s state:

\[
(x_{\text{proj}}, y_{\text{proj}}) = ((x + d_x) \mod w,\ (y + d_y) \mod h)
\]

Useful for checking legality of moves or evaluating future states in decision-making extensions.

---

### 5. Deposition: `deposit(local_grid)`

Upon successfully moving to a new cell, the agent deposits a fixed quantity of chemoattractant:

\[
\text{local\_grid}[y_{\text{int}}, x_{\text{int}}, 1] += \text{depT}
\]

where:

\[
x_{\text{int}} = \lfloor x \rfloor,\quad y_{\text{int}} = \lfloor y \rfloor
\]

This models the *sol-gel transition* behavior of Physarum, wherein forward movement of the gel phase is accompanied by localized deposition of attractant material that reinforces paths for subsequent agents.

Note: The current implementation comments out deposition to channel 0 (presumably reserved for food or obstacle markers), and only updates channel 1 (trail layer).

---

### 6. Reorientation: `reorient()`

Invoked when an agent fails to move (typically because the projected cell is occupied). The agent chooses a completely new random direction:

\[
\vec{d}_{\text{new}} = (\cos(\theta'),\ \sin(\theta')), \quad \theta' \sim \mathcal{U}(-\pi, \pi)
\]

This introduces stochasticity into the system and prevents agents from becoming stuck indefinitely in blocked locations.

---

## ⚙️ Summary of Key Parameters

| Parameter | Symbol | Description |
|----------|--------|-------------|
| `SO`     | \( d_s \) | Sensor offset distance (in grid cells) |
| `SA`     | \( \alpha \) | Half-angle of the sensor field (radians) |
| `RA`     | \( \Delta_\theta \) | Angular rotation per unit attractant gradient |
| `depT`   | \( \delta \) | Amount of attractant deposited per successful move |

These parameters critically influence emergent phenomena such as path reinforcement, convergence speed, and network connectivity.

---

## 🌀 Behavior Notes

- Agents exhibit **autocrine chemotaxis**: they both sense and deposit the same chemoattractant signal.
- The 3-sensor design with forward bias and large offset (`SO`) encourages **local alignment**, leading to cohesive swarm-like behavior.
- Periodic boundary conditions allow exploration across edge boundaries without loss of continuity.
- Random initial directions and reorientation ensure **diversity in global exploration**, while gradient tracking enables **local exploitation** of favorable paths.

---

Let me know if you’d like a similar breakdown of the global simulation loop or visualization code, or if you want this turned into LaTeX-formatted math for a paper!

Yes, definitely. Since you've already included the algorithm in **Algorithm \ref{code:flow}**, you don’t need to repeat all the implementation details in the _Overall System Flow_ subsection. Instead, you can summarize the process at a higher level, referring readers to the algorithm for specifics.

Here’s a concise rewrite of your **\subsection{Overall system flow}**:

Here’s a more concise and focused rewrite of your **Design and Implementation** subsection:

---

\subsubsection{Design and Implementation}

To assess the impact of diffusion techniques on emergent trail formation in the _P. polycephalum_ simulation, we tested three alternative filters: Gaussian Blur, Bilateral Filter, and a Custom Weighted Blur, each replacing the default uniform blur. These were chosen to explore whether biologically or perceptually inspired filters produce qualitatively different trail dynamics.

Each filter processes the trail map (second grid channel) before applying exponential decay. The Gaussian blur applies isotropic smoothing; the Bilateral filter preserves edges by weighting spatial and intensity similarity; the Custom blur uses a hand-tuned kernel approximating a centralised Gaussian.

Simulations were run with identical parameters (see \ref{tab:defaultparams}), except for a reduced step count of 750 to enable faster iteration. A fixed random seed ensured reproducibility, and kernel size $k = 3$ was used across all methods for consistency.

---

Let me know if you'd like an even tighter summary or if this will be followed by analysis results.


Core of Meshtstic because this is ow the meshtastic simulatior works and our works in the same way, so it cannot be worse than that as it uses the same conclusions. 



IMPORTANT:


\section{Method / Description}
The highlevel is described, as well as the graphical look of the decay functions and maths equations in the main report but implemneation details below.

Meshtastic's default broadcast flooding means all nodes in range receive and rebroadcast a message, provided the hop limit allows it. My implementation alters this behaviour by introducing a probability-based rebroadcast check based on either the Neighbour Node Count or Signal to Noise Ration (SNR). When a node receives a packet, it calculates a forwarding probability based on local conditions and only rebroadcasts if this value exceeds a randomly sampled threshold as seen in Figure x. 

The packet strucure as shown in \ref{tab:meshtastic-packet}, shows very little room for extra telemetry to be added as such, we had to work with what was available only.

\calculate neighbour count
 note that due to broadcast natureof it, if a node forward a packet, it doesnt add its unique id to the packet, all it does it decrements the hop limit. As such, to calculate neighbour count, you have two options, either  see a message where hop limit = hop start in the packet meaning the it is right next to you or you can get it by seeing who sends you ackdwoledgemnts. But the hop limit has a problem because it just lets you know that a packet was sent by a neighbour but not which neighbour / how many neighbours do you have. Since if a node forwards a packet that its not its own / broadcasts it, it will receive ackwonledgems from every node that received that packet form the first time. So if a node is active/ often forwards packets, it will have a accurate representation of each node ID that sent it ackwoledgements so it will know its neighbours.

To calculate the neihgbour count, the dfault node memory had to be changed, it had to include an additional hashmap which will store 
both the id of nodes it has seen (sent it ackwoledgments) and utc time of when it was added. Since the maximum nodes a node can see in Meshtastic is 50, the max size was 50 entries, and if it overlfew, it owuld remove the oldest entry. 
Time from interal clock of the hardware. When the node sees a new node id not in seenNodes, then it appends that id to the hasmap and the time of seeing it. When you see the same node thats aclready in hashmap, you only update the time in the hashmap. this provided an accurare account based on packets you receive of the nearby node in your area. Reasoning for the time is that nodes in Meshtastic are uniquely mobile, so they go off an on often and often move around. as such, if a node is turned on for a long time, its seenNodes might be heabily populated even though most nodes have moved away from its range or been simply turned off. As such, neigbour count decay functions work best than seenNodes is as accurate as it can, so it does this. If 3 hours have passed since you last saw the node, you remove it from the seenNodes hashmap. 

\neighbour count countolled flooding
Once you have a neighbour count, you can convert that to a probability based on the decay functions described in the main report, which test differnt probablities equaiton relations (various curves) to see if any of them perform better, is there an optimal decay function because there are many to hcoose from. 

\snr
SNR get attached into each packet that is sent and recalculated when forwaded so when a node receives any packets and decides if it rebroadcast or not, it fetches the snr value from packet and applies the mapping function as defined in the main report to get a probablitlity. 

\implementation
To implement these, one had to create a new node model to be used by the simulator. This required to modify the ReliableFlood class provided by meshtastic and modify the rebroadcast login.

If there is a error so neighther neithbour count or snr cannto be fetched from the packet, then the routing defaults back to Managed Flooding so it always rebroadcasts.

\critical bridge problem.
As seen in the math equatiosn in the report, there is often a min 2, so if only 2 nodes or less are in your hashmap, then you always rebroadcast. Reasoning for this is that this algorithm suffers from from critical bridge problem, where a node will not reboadcast even though it is a critical link between two subsets of nodes, which would cause the ssender to rebroadaast again and again until it fianlly goes through and tha tnode receives an ackwolodment. In those scenarios, latency could heabily increwase. As such, setting the probability like that higher helps to avoid this problems. 
Normally this can introcude a problem of congestion if in those cases nodes always rebroadcast (which this routing algo should help with as detailed in the report) but if one seens only 1-2 other nodes, then they would have to rebroadcast very frequenctly and quickly, which meshtastic attempts to block anyways as it treates it as spam (message duplicationn dont rebroacast). 

However, this is a oversimplification. As if a node is a critical bridge requires to know thte topology of the network and ranges of nearby nodes, which requires centralisation, whihc our nodes cannot afford computationally and goes againts the principles of this routing algorithm. As such, the 2 or less nodes always rebroadcast rule is artirtaby and mode interesting solution could be proposed here. 


\finetuning. 
Many of the routing stuff mentioned parameters, like Exponetial decay with an alpha. These wqere calculated by running tests on the simulator (aggredated) to find the most optimal values. 

Note that although the computation wasnt expensive, it was tested on the hardware and it showed no disgnifiant increase in computational overhead. There were other decays that did and as such didnt make the cut. 




Note:
5 minute presentation and 3 minutes q&a I think.

| Slide | Title                            | Time (secs) | Purpose                                                             |
| ----- | -------------------------------- | ----------- | ------------------------------------------------------------------- |
| 1     | Title slide                      | 10 sec      | Introduce yourself and the project title briefly                    |
| 2     | What is _Physarum Polycephalum_? | 30 sec      | Introduce organism and relevance                                    |
| 3     | Problem Statement                | 30 sec      | Define the core problem clearly (10 marks for this!)                |
| 4     | Why do we want accurate models?  | 30 sec      | Motivation and applications                                         |
| 5     | Our model inspiration            | 30 sec      | Reference to previous models/literature (background, 10 marks)      |
| 6     | Methodology                      | 40 sec      | Core design: rules, agent behavior, system layout (biggest weight)  |
| 7     | Results – 13000 Steps Simulation | 40 sec      | Visual outcomes, briefly comment on performance/results             |
| 8     | Experimentation (title slide)    | 5 sec       | Quick transition only                                               |
| 9-12  | Experiments 1–4                  | 80 sec      | Focus on 2-3 key insights across experiments; don’t overload detail |
| 13    | Conclusion                       | 25 sec      | Wrap-up main points and implications                                |
| 14    | Questions                        | 2 min       | Practice some likely Q&A ahead of time                              |
- 

- A plasmodial slime mould with no brain or nervous system.
- Known for surprising problem-solving abilities despite decentralisation.
- Demonstrates **emergent behaviours**: optimal pathfinding and efficient network formation (e.g., between food sources).
- Examples of real-world mimicry: railway design, maze solving.
To visualize your custom kernel, we can represent it as a table, showing the values of the kernel matrix itself. Your kernel is a 3x3 matrix:

$$
\begin{bmatrix}
1 & 2 & 1 \\
2 & 4 & 2 \\
1 & 2 & 1
\end{bmatrix}
$$

This kernel is typically used for image processing tasks like convolution. It looks like a "box blur" kernel, where each value corresponds to the weight assigned to the pixel in the convolution process.

Here’s a simple table representing it:

|           | **Column 1** | **Column 2** | **Column 3** |
| --------- | ------------ | ------------ | ------------ |
| **Row 1** | 1            | 2            | 1            |
| **Row 2** | 2            | 4            | 2            |
| **Row 3** | 1            | 2            | 1            |

This table visualizes the kernel’s values. You can apply this kernel to an image using convolution, where each pixel's new value is calculated by multiplying corresponding kernel values with the pixels in the image and summing them up.

Would you like to see an example of how this kernel would be applied to an image, or how to perform the convolution in code?
