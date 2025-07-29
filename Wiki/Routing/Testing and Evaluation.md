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


Yes, the idea of dynamically adjusting the flooding probability based on local node density is a well-known optimization in probabilistic flooding and has been explored in several papers — often falling under "adaptive probabilistic flooding." Your intuition aligns with that literature: sparse regions should favor higher forwarding probabilities, while dense ones can reduce redundancy.

Let’s dive into how you can formulate this.

---

### 🔧 **Dynamic Probability Based on Neighbor Count**

Let:

- nn be the number of _unique_ neighbors seen by a node.
    
- P(n)P(n) be the forwarding probability.
    

You can design P(n)P(n) such that:

- P(n)=1P(n) = 1 when n≤2n \leq 2 (critical nodes).
    
- P(n)<0.7P(n) < 0.7 as n→∞n \to \infty (dense zones).
    

A few candidate functions:

---

### 📈 1. **Inverse Linear Decay**

$$P(n) = \min\left(1, \frac{k}{n}\right)$$$

Where:

- kk is a constant — e.g. k=2k = 2 or k=3k = 3
    
- Ensures P(n)=1P(n) = 1 for n=2n = 2, and decays afterward
    

_Example:_

- n=2⇒P=1n = 2 \Rightarrow P = 1
    
- n=10⇒P=0.2n = 10 \Rightarrow P = 0.2
    

Add a minimum bound like max⁡(P(n),0.3)\max(P(n), 0.3) to prevent under-flooding.

---

### 📉 2. **Exponential Decay**

$$P(n) = \max\left(P_{\text{min}}, \exp(-\alpha n)\right)$$

- α\alpha controls how fast it decays.
    
- PminP_{\text{min}} is a safety floor, e.g., 0.2.
    

You can tune α\alpha to get P(2)≈1P(2) \approx 1, then decay smoothly.

---

### 🧮 3. **Logarithmic Decay**

$$P(n) = \frac{1}{\log(n + 1)}$$

This grows slower and gives a more gentle slope, but you’ll want to normalize it (scale it to max 1, min around 0.3).

---

### ⚖️ 4. **Piecewise Hybrid Function**

$P(n) = \begin{cases} 1 & \text{if } n \leq 2 \\ 0.7 & \text{if } n = 3 \\ \frac{0.7}{\log(n)} & \text{if } n > 3 \end{cases}$

This blends familiarity with practical limits and gives you full control.

---

### 🧪 Has This Been Done Before?

Yes — several papers explore this under various names:

- **Adaptive Probabilistic Flooding**
    
- **Degree-based Flooding**
    
- **Neighborhood-aware Gossiping**
    

**Relevant papers you could skim:**

- _"A probabilistic routing protocol for intermittently connected networks" – Jain et al._
    
- _"An adaptive probabilistic flooding scheme for mobile ad hoc networks" – Ni et al._
    
- _"A survey of broadcast schemes for mobile ad hoc networks"_ – good literature summary.
    

---

### ✅ Final Thoughts

I recommend:

- Start with a hybrid function (like piecewise or log-based).
    
- Cap it with a floor value to avoid total loss in dense networks.
    
- Measure delivery rate vs redundancy (e.g., using Meshtastic’s hop count, RSSI, or TTL).
    

Also, feel free to tune based on:

- **Battery level** (if low, reduce flooding chance)
    
- **Historical success rate** (i.e., how many times a retransmission actually led to a new node receiving the packet)
    

Want me to write a function prototype in Python or C++ for Meshtastic?


Report thins: !!!! note, min is 0.3 but can be smaller, play with it maybe. 
Inverse Decay
![[Pasted image 20250422222446.png]]

Sure! With your modification — setting P(n)=1P(n) = 1 for n≤2n \leq 2 — the final math equation becomes:

$$P(n) = \begin{cases} 1 & \text{if } n \leq 2 \\ \max\left(P_{\min}, e^{-\alpha n}\right) & \text{if } n > 2 \end{cases}$$

Where:

- Pmin⁡=0.3P_{\min} = 0.3
    
- α∈[0.1,1.0]\alpha \in [0.1, 1.0]
    
- n∈Nn \in \mathbb{N} (typically 1≤n≤101 \leq n \leq 10).
    

Let me know if you'd like it rendered in LaTeX or used in a graph!


Absolutely! With your adjustments (capping at 1 and setting P(n)=1P(n) = 1 for n≤2n \leq 2), the final math equation is:
$$P(n) = \begin{cases} 1 & \text{if } n \leq 2 \\ \max\left(0.3,\ \min\left(1,\ \frac{1}{\ln(n + 1)}\right)\right) & \text{if } n > 2 \end{cases}$$



Nice — you've already nailed the **big four** probabilistic decay strategies (linear, inverse, logarithmic, exponential), which are the most widely referenced in flooding optimization literature. But if you're up for squeezing out more insights or experimenting with creative edge-case handling, here are a few **niche or underused probability decay strategies** that could offer **unexpected benefits** — especially in **unpredictable, lossy mesh networks** like Meshtastic:

---

## 🧠 Alternative/Niche Decay Functions

### 1. **Sigmoid Decay**

- Smooth ramp-up at first, then quickly decays after midpoint.
    
- Good for "boosting" middle-density nodes while suppressing high-density spam.
    

$$P = \frac{1}{1 + e^{k(n - n_0)}}$$

- `n` = number of neighbors
    
- `n₀` = center point (e.g. 5)
    
- `k` controls the sharpness
    

> 🎯 Use case: Emphasizes “bridge” nodes in moderate density; reduces spam in high density.

---

### 2. **Step or Threshold Decay (Cliff Function)**

- Simple but effective: if neighbors ≤ threshold → always rebroadcast, else never.
    

```python
P = 1.0 if n <= T else 0.0
```

- Or with a grace margin:
    

```python
P = 1.0 if n <= T else 0.3
```

> 🚪 Use case: Predictable and easy to reason about — perfect for tuning hard cutoffs in sparse deployments.

---

### 3. **Quadratic or Polynomial Decay**

- Falls off faster than linear but gentler than exponential.
    

P=1n2orP=1an2+bn+cP = \frac{1}{n^2} \quad \text{or} \quad P = \frac{1}{an^2 + bn + c}

> 📉 Use case: May perform better than exponential in high-density networks while not completely killing mid-density nodes.

---

### 4. **Randomized Floor Decay**

- Add **non-zero minimum** to avoid packet "black holes."
    

P=max⁡(0.2,1n)P = \max(0.2, \frac{1}{n})

- Sometimes useful in networks where critical messages **must** keep propagating, even in dense areas.
    

> 🔁 Ensures persistence with low power cost.

---

### 5. **Harmonic Mean / Mean-Based**

- Combine neighbor count with another variable like RSSI or SNR:
    

P=21n+1SNRP = \frac{2}{\frac{1}{n} + \frac{1}{\text{SNR}}}

> 📡 Use case: Useful for **balancing two competing metrics** (connectivity and signal quality).

---

### 6. **Entropy-Inspired Decay**

- Not commonly used, but you can use **local entropy** of packet arrival times or neighbor ID changes as a proxy for network volatility:
    

P=H(t)⋅1nP = H(t) \cdot \frac{1}{n}

Where H(t)H(t) is a time-windowed entropy function of neighbor stability.

> 🧬 Use case: Helps in **mobile or semi-mobile** networks (like drones, cars).

---

### 7. **Time-Based Decay (Dynamic Flood Delay)**

- Instead of pure probability, adjust the **time before rebroadcast**, giving denser nodes more time to hear a rebroadcast and suppress their own.
    

T=k⋅log⁡(n)T = k \cdot \log(n)

> ⏱ Use case: Can avoid simultaneous rebroadcasts from close nodes.

You can combine this with probabilistic rebroadcast or use as a standalone **rebroadcast suppression mechanism**.

---

## ✅ Should You Move On to SNR, Seen-Count, TTL, etc.?

Since you’ve covered the most impactful mathematical decay models, it’s a **great time to start combining them with environmental factors**, like:

- **SNR / RSSI / Link quality**
    
- **Node location awareness (if available)**
    
- **Message type or TTL**
    
- **Packet redundancy detection**
    
- **Node role (battery-powered vs relay station)**
    

The decay function determines **when to rebroadcast**, but these contextual parameters help decide **whether** to rebroadcast at all — leading to smarter, situation-aware routing.

---

### 🎯 TL;DR Suggestion

- ✅ Keep your 4 core decay models + add 1–2 niche ones (like sigmoid or threshold) for coverage.
    
- 🎛 Create a flexible framework to _combine_ decay with **dynamic conditions** (RSSI, SNR, redundancy).
    
- 📊 Then measure PDR, hops, and latency in each mode to find sweet spots.
    

---

Want a template or config idea to test these side-by-side automatically and log stats?

Nice refinement! If you want the logistic decay but force P(n)=1P(n) = 1 for n≤2n \leq 2, the final system becomes a **piecewise logistic function** with a cap:

---

### ✅ **Final Math Equation**:

$$P(n) = \begin{cases} 1 & \text{if } n \leq 2 \\ \frac{1}{1 + e^{k(n - n_0)}} & \text{if } n > 2 \end{cases}$$

Where:

- k>0k > 0: controls steepness of the drop,
    
- n0n_0: center point of decay (e.g., n0=10n_0 = 10).
    

---

### 🐍 Python Code to Test It:

```python
import math

def P(n, k, n0):
    if n <= 2:
        return 1
    return 1 / (1 + math.exp(k * (n - n0)))

# Test values of k
ks = [round(0.2 * i, 1) for i in range(1, 6)]  # 0.2 to 1.0
n0 = 10

# Print header
print(f"n\\k\t" + "\t".join([f"{k:.1f}" for k in ks]))
for n in range(1, 21):
    print(f"{n}\t", end="")
    for k in ks:
        print(f"{P(n, k, n0):.3f}", end="\t")
    print()
```

Let me know if you want to clamp it with a min or plot it out!