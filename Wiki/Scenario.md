**Scenario Overview**:

Reliable long-range communication in remote areas remains a significant challenge, particularly for individuals without access to licensed radio equipment or cellular infrastructure. In emergency situations, the ability to send distress signals or coordinate rescue efforts is critical. However, traditional communication networks often fail in such environments due to limited infrastructure coverage.

APRS (Automatic Packet Reporting System) is widely used for emergency communication, particularly by first responders, search-and-rescue teams, and amateur radio operators in the US and UK. However, APRS transmission requires an amateur radio licence, limiting its accessibility to the general population. Additionally, APRS infrastructure has coverage gaps in many remote regions (coverage maps available on [aprs.fi](https://aprs.fi/)).

**Problem Statement**:

Consider a scenario where a person in distress (such as a lost hiker in a mountainous region, a remote field researcher, or an offshore sailor) needs to send an emergency message. Without cellular service, they cannot contact emergency responders directly.

A potential alternative is Meshtastic, an open-source, low-power mesh network protocol that operates on licence-free ISM bands (e.g., 915 MHz in the US, 868 MHz in Europe). Meshtastic allows users to send messages across a distributed network of relay nodes. However, Meshtastic’s current routing mechanism, managed flooding, lacks reliability guarantees, making it unsuitable for critical emergency communication.

**Challenges and Limitations**:

1. Protocol Incompatibility –> Meshtastic and APRS operate on different protocols, requiring a gateway for intercommunication. While APRS-to-internet gateways exist, no APRS-to-Meshtastic gateway is currently available for radio-based communication.
2. Licensing Constraints –> While APRS is widely used in emergency response, its licensing restrictions prevent unlicensed individuals from transmitting directly. Meshtastic is accessible but lacks integration with APRS networks.
3. Routing Inefficiencies –> The reliability of message delivery in Meshtastic is highly dependent on network density. Managed flooding can cause network congestion, increased latency, and excessive battery consumption, making it unsuitable for emergency use in low-connectivity scenarios.

**Research Goals**:

This research aims to enhance the reliability of Meshtastic’s message delivery in emergency scenarios by:

- Developing an APRS-Meshtastic gateway to allow unlicensed users to send emergency messages over APRS.
- Analysing and optimising routing mechanisms to improve message reliability, particularly in low-connectivity environments.
- Creating a simulation environment to evaluate real-world performance under different conditions, with procedurally generated test cases representing various emergency scenarios.

**Potential Use Cases**:

- Wilderness Emergencies – A hiker lost in a remote national park where APRS infrastructure does not reach.
- Maritime Distress Signals – A sailor needing to send an SOS from a small boat beyond mobile network coverage.
- Natural Disaster Communication – People trapped in disaster-stricken areas where infrastructure has been damaged.
- Expedition Coordination – Scientists or explorers in isolated locations needing to maintain contact with base camps.

By addressing these gaps, this study aims to bridge the communication divide between Meshtastic and APRS, making emergency messaging more accessible and reliable for off-grid users.

**Why reliability is important here:**

Reliability is critical in this context because an SOS message is often a last resort in life-threatening situations. The ability to consistently deliver a distress signal to first responders can mean the difference between rescue and prolonged danger. Several factors highlight why reliability is essential:

- Life-Saving Communication – In emergencies, every second counts. A failed or delayed message could result in serious harm or loss of life.
- Limited Retrying Opportunities – Unlike traditional networks where a sender can simply resend a message if it fails, individuals in remote locations may have limited power, time, or mobility to keep attempting communication.
- Network Instability – Meshtastic nodes operate in a dynamic environment where nodes may move, power off, or have varying signal strength. A routing mechanism that prioritises reliability ensures messages are delivered despite these challenges.
- Harsh Environmental Conditions – In wilderness, maritime, or disaster scenarios, users may be in extreme conditions (cold, heat, high altitudes, etc.), making it impossible for them to manually troubleshoot failed transmissions.
- Lack of Alternative Communication Methods – In many cases, no other communication method is available—no mobile network, satellite link, or functional radio infrastructure—making the reliability of the mesh network the only lifeline.

Given these constraints, improving message success rates, reducing latency, and ensuring delivery under adverse conditions are key goals for this research.

**Example:**
![[IMG_6078.jpg]]

Notes:
For this scenario, the hiker has no effect on where the gateway is in relation to him when he needs to transmit a SOS, so is gateway location a testable part of this project? I don't think so but it would still be nice. 
See testing. 


What kind of graphs would be nice for the draft? 
