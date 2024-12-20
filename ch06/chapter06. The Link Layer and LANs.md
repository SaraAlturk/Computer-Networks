## 6.1 Introduction to the Link Layer

- Any device that runs a link-layer protocol is a node
  
- nodes: hosts, routers, switches, access points
  
- The communication channels that connect adjacent nodes along the communication path are referred as links. (wired , wireless. LANs)

- layer-2 packet: frame, encapsulates datagram from network layer
  
- Purpose: Transfer datagrams (from the network layer) between physically adjacent nodes over a link. Over a given link, a transmitting node encapsulates the datagram
in a link-layer frame and transmits the frame into the link.

<p align="center">
  <img width="400" alt="Screenshot 2024-12-02 at 8 52 12 PM" src="https://github.com/user-attachments/assets/2dd365e6-5f6f-4065-bd8c-f7ab95c4b8ee">
</p>

- A datagram (layer 3 data unit) can travel over multiple types of links in a network, each using a different link-layer protocol;WiFi on first link, Ethernet on next link

- Each link's protocol defines how data is formatted, transmitted, and managed for that specific medium.

- Different link-layer protocols provide different sets of services.(protocols may not provide reliable data transfer, leaving error handling to higher layers)

### 6.1.1 The Services Provided by the Link Layer
**1. Framing** Almost all link-layer protocols encapsulate each network-layer data-gram within a link-layer frame before transmission over the link.

- A frame consists of a data field, in which the network-layer datagram is inserted, and a number of header fields.
The structure of the frame is specified by the link-layer protocol.

**2. Link access** A medium access control (MAC) protocol specifies the rules by which a frame is transmitted onto the link.

- No problem in point-to-point links. The problem occurs when multiple nodes share a single broadcast link—the so-called multiple access problem.

**3. Reliable delivery** When a link-layer protocol provides reliable delivery service, it guarantees to move each network-layer datagram across the link without error.
link-layer reliable delivery service can be achieved with acknowledgments and retransmissions

- A link-layer reliable delivery service is often used for links that are prone to high error rates, such as a wireless link, with the goal of correcting an error locally—on the link where the error occurs—rather
than forcing an end-to-end retransmission of the data by a transport- or application-layer protocol.

- A link-layer reliable delivery can be considered an **unnecessary** overhead for low bit-error links, including fiber, coax, and many twisted-pair copper links.

**5. Error detection and correction** The link-layer hardware in a receiving node can incorrectly decide that a bit in a frame is zero when it was transmitted as a one, and vice versa. Such bit errors are introduced by signal attenuation and electromagnetic noise.

- Error correction is similar to error detection, except that a receiver not only detects when bit errors have occurred in the frame but also determines
exactly where in the frame the errors have occurred (and then corrects these errors) without retransmission.

**6. Flow Control**
Maintains a balance between the sending and receiving nodes to prevent data loss or buffer overflow.
Ensures that the receiver can handle data at the rate it's being sent.

**7. Half-Duplex and Full-Duplex Communication**

- Half-Duplex: Both nodes can send and receive data, but not at the same time (e.g., walkie-talkies).
  
- Full-Duplex: Both nodes can send and receive data simultaneously (e.g., Ethernet, modern Wi-Fi).

### 6.1.2 Where Is the Link Layer Implemented?
<img width="390" alt="Screenshot 2024-12-02 at 10 09 09 PM" src="https://github.com/user-attachments/assets/3891e701-e3d2-4672-b52c-c1b168292530">

- The link layer exists in all devices that are part of a network (hosts like computers, smartphones, etc.).

- The link layer is implemented on a chip called the network adapter, also sometimes known as a network interface controller (NIC).
These cards combine both link layer and physical layer functionalities.

- The NIC is connected to the host’s system via a host bus (e.g., PCI or PCIe slots).

- The software components of the link layer implement higher-level link-layer functionality such as assembling link-layer addressing information and activating the controller hardware.

- A special-purpose chip on the NIC handles most link-layer tasks such as:Framing, Link access (MAC rules), Error detection, Physical layer interface (e.g., signaling)

<img width="600" alt="Screenshot 2024-12-02 at 10 17 37 PM" src="https://github.com/user-attachments/assets/82a4c8a0-9d84-4aae-8394-f63115151da8">


## 6.2 Error-Detection and -Correction Techniques

<p align="center">
 <img width="500" alt="Screenshot 2024-12-02 at 10 56 05 PM" src="https://github.com/user-attachments/assets/08775cab-7bac-4952-9432-2cb52d111951">
</p>

- Error-detection and -correction techniques allow the receiver to sometimes, but not always, detect that bit errors have occurred.
  
- Even with the use of error-detection bits there still may be undetected bit errors; that is, the receiver may be unaware that the received information contains bit errors.
A consequence, the receiver might deliver a corrupted datagram to the network layer, or be unaware that the contents of a field in the frame’s header has been corrupted.

### 6.2.1 Parity Checks
The simplest form of error detection is the use of a single parity bit.
<p align="center">
 <img width="300" alt="Screenshot 2024-12-02 at 10 55 00 PM" src="https://github.com/user-attachments/assets/c12e5666-7725-4432-a419-b34420f69547">
</p>

- In an even parity scheme, the sender simply includes one additional bit and chooses its value such that the total number of 1s in the d + 1 bits (the original information plus a parity bit) is even.

- For odd parity schemes, the parity bit value is chosen such that there is an odd number of 1s.

**Summary of Sender and Receiver Responsibilities for Single-Bit and Two-Dimensional (2-D) Parity**

| **Parity Type**           | **ex**                                                                                           | **Sender Side**                                                                                     | **Receiver Side**                                                                                          |
|---------------------------|----------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------|
| **Single-Bit Parity**      | <img width="300" alt="Screenshot 2024-12-02 at 10 55 00 PM" src="https://github.com/user-attachments/assets/c12e5666-7725-4432-a419-b34420f69547"> | - Adds a parity bit to the data.<br> - Ensures **even parity** (total number of 1s is even).       | - Computes parity of received bits (data + parity).<br> - If parity is not even, detects an error.         |
| **Two-Dimensional Parity** | <img width="300" alt="Screenshot 2024-12-02 at 11 05 49 PM" src="https://github.com/user-attachments/assets/31308291-6270-4e7c-8b55-a656a0bc318b"> | - Organizes data into a matrix.<br> - Calculates and appends **row parity** (for each row).<br> - Calculates and appends **column parity** (for each column). | - Checks parity for each row and column.<br> - **Single-bit error**: Identifies row and column with mismatched parity, corrects it.<br> - **Two-bit error**: Detects it but cannot correct. |


- The ability of the receiver to both detect and correct errors is known as forward error correction (FEC).

### 6.2.2 Checksumming Methods  
- In checksumming techniques, the d bits of data are treated as a sequence of k-bit integers.

- One simple checksumming method is to simply sum these k-bit integers and use the resulting sum as the error-detection bits.

- An error-detection technique used widely in today’s computer networks is based on cyclic redundancy check (CRC) codes. CRC codes are also known as polynomial codes, since it is possible to view the bit string to be sent as a polynomial whose coefficients are the 0 and 1 values in the bit string, with operations on the bit string interpreted as polynomial arithmetic.

- Checksums involve summing up the values of a set of data units and appending the result to the data being transmitted.

- Checksum Calculation: Sender calculates a checksum value based on the data. Sender sends both the data and the checksum. Receiver calculates its checksum on the received data.

**Summary Table of Checksum Process**

| **Process**             | **Sender Side**                                                                                             | **Receiver Side**                                                                                       |
|-------------------------|------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
| **Goal**                | Detect errors in the transmitted segment (e.g., flipped bits).                                             | Detect errors in the received segment by verifying checksum.                                            |
| **Data Handling**       | Treat contents of the UDP segment (including UDP header and IP addresses) as a sequence of **16-bit integers**. | Treat the received segment as a sequence of **16-bit integers**, including the received checksum.       |
| **Checksum Calculation**| - Add all 16-bit integers using **one’s complement arithmetic**.<br>- Compute the **one’s complement** of the result (flip bits). | - Recompute the checksum of the received segment using **one’s complement arithmetic**.                |
| **Checksum Field**      | Place the computed checksum value into the **UDP checksum field** of the segment.                         | Compare the recomputed checksum with the checksum value in the received segment.                       |
| **Error Detection**     | Transmit the segment with the checksum.                                                                  | - **If not equal:** Error detected.<br>- **If equal:** No error detected (but some errors may go undetected). |


### 6.2.3 Cyclic Redundancy Check (CRC)
- more powerful error-detection coding
  
- It uses a polynomial division method to check for errors in data transmission.

- Key Components of CRC:
  
    <img width="350" alt="Screenshot 2024-12-02 at 11 41 32 PM" src="https://github.com/user-attachments/assets/b9fe800b-e40f-41a1-be74-8ffdff316820">

   - D (Data Bits): Represents the binary data being sent.

   - G (Generator Bit Pattern):A predetermined binary sequence (polynomial) with  r+1 bits used for the division. (Agreed upon by both the sender and receiver).
     
   - R (CRC Bits): r-bit checksum value calculated and appended to the data for error checking.

**Summary Table of CRC Process**

| **Stage**           | **Sender Side**                                                                                         | **Receiver Side**                                                                                          |
|---------------------|-------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------|
| **Input**           | Data bits **D** and generator polynomial **G**.                                                       | Received data and CRC bits **D, R**.                                                                      |
| **Process**         | - Append **r** zeros to **D**.<br> - Compute remainder **R** using mod-2 division.<br> - Transmit **D, R**. | - Divide received frame (**D, R**) by **G**.<br> - Check if remainder = 0.                                |
| **Output**          | **D, R** (data + CRC bits).                                                                            | - If remainder = 0: Data is error-free.<br> - If remainder ≠ 0: Error detected.                           |
| **Key Advantages**  | - Efficient for detecting burst errors.<br> - No retransmission needed for detection.                  | Detects errors efficiently for shorter data burst lengths.                                                |

**CRC Example**

<img width="456" alt="Screenshot 2024-12-02 at 11 45 37 PM" src="https://github.com/user-attachments/assets/c3369914-fa16-42c2-adba-f2d9d9a6afc6">



## 6.3 Multiple Access Links and Protocols

- There are two types of network links: point-to-point links and broadcast links.

|                | **Point-to-Point Links** | **Broadcast Links**                            |
|----------------|--------------------------------|------------------------------------------------------|
| **Characteristics** | Connects two devices directly. Dedicated communication link between them. | Single communication channel shared by multiple devices. Data sent by one device is received by all others. |
| **Link Access Control** | Since it's a point-to-point link, there's no contention for the link. No need for a MAC protocol for multiple access. | Requires a MAC protocol to coordinate access among multiple devices. Avoids collisions and ensures fair access. |
| **Efficiency** | Efficient use of bandwidth since only two devices share the link. Full capacity available to each device. | Bandwidth is shared among multiple devices. Efficiency depends on the effectiveness of the MAC protocol. |
| **Examples** | Traditional telephone lines and Point-to-point leased lines. link between Ethernet switch, host.  (PPP) for dial-up access | Ethernet networks (using CSMA/CD or CSMA/CA protocols). 802.11 wireless LAN, 4G/4G. satellite |


- Problems with Multiple Access:
  1. Collision/interference : Occurs when two or more nodes transmit at the same time, resulting in interference.
     
     - Consequences of collisions: Data cannot be understood by the receiver. Channel bandwidth is wasted. Higher number of nodes leads to more collisions, further reducing efficiency.
     
  2. Coordination: Nodes need a way to share the channel without an out-of-band communication channel for coordination.

- A multiple access protocol is a distributed algorithm that determines how nodes in a network share a single broadcast channel (e.g., Ethernet or Wi-Fi).
  
- It manages when a node can transmit to avoid collisions and ensure efficient use of the channel(no-out-of band coordination)


Ideal Characteristics of a Multiple Access Protocol
* Scenario: A multiple access channel (MAC) operates at a rate R bps (bits per second).
* Desiderata:
    1. When one node wants to transmit, it can send at the full rate R
    2. 2. when M nodes want to transmit, each can send at average rate R/M
    3. Fully decentralized:
        * No centralized coordinator or node for managing transmissions.
        * No need for global clock synchronization.
    4. Simple:
        * The protocol should be straightforward and easy to implement.


### 6.3.1 Channel Partitioning Protocols

- Classification of multiple access protocols into three categories: 
  - channel partitioning:  Divide the channel into smaller, exclusive “pieces” such as:
     - Time slots (Time-Division Multiple Access, TDMA).
     - Frequency bands (Frequency-Division Multiple Access, FDMA).
     - Codes (Code-Division Multiple Access, CDMA).

  - random access: The channel is not divided; nodes can transmit at any time.Collisions are allowed but mechanisms are provided to recover from them.
    
  - taking-turns (controlled access): Nodes take turns transmitting data. Nodes with more data to send may take longer turns.

**Summary Table of Multiplexing Techniques**
| **Multiplexing Technique**      | **How It Works**                                                                                 | **Advantages**                                           | **Disadvantages**                                                                                           | **Image**                                                                                       |
|---------------------------------|-------------------------------------------------------------------------------------------------|---------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------|
| **Time-Division Multiplexing (TDM)** | - Divides time into frames, each with **N** time slots.<br>- Each slot is assigned to a node, allowing nodes to transmit in turn.<br>- Unused slots go idle. | - Fairness: Each node gets a turn.<br>- No collisions.  | - Bandwidth wasted if slots go unused.<br>- Limited throughput: Node limited to **R/N**, must wait for turn even if the only one with packets. | <img width="600" alt="Screenshot 2024-12-03 at 4 21 58 AM" src="https://github.com/user-attachments/assets/86fea2e7-a968-45ec-b27b-dfe2635bbfec"> |
| **Frequency-Division Multiplexing (FDM)** | - Divides the channel into **N** frequency bands.<br>- Each node is assigned a specific frequency band.<br>- Unused transmission time in frequency bands goes idle. | - No collisions.<br>- Fairness in bandwidth allocation. | - Bandwidth wasted if a frequency band is unused.<br>- Limited throughput: Node limited to **R/N**, must wait for turn even if the only one with packets. | <img width="600" alt="Screenshot 2024-12-03 at 4 23 41 AM" src="https://github.com/user-attachments/assets/a8b12f5a-f408-4a16-ac23-4ffa47f1d739"> |
| **Code Division Multiple Access (CDMA)** | - Assigns unique codes to each node.<br>- Nodes encode their data with their code, enabling simultaneous transmissions.        | - Allows simultaneous transmissions.<br>- Reduces interference through unique codes. | - Requires complex encoding/decoding.<br>- Performance may degrade with a large number of nodes.                                     | N/A                                                                                           |


### 6.3.2 Random Access Protocols

- Allows nodes to transmit data when they have packets to send without prior coordination.
  
   - A node transmits at the full channel data rate 𝑅
     
   - Collisions occur if two or more nodes transmit simultaneously.
     
- In a random access protocol, a transmitting node always transmits at the full rate of the channel (R bps). Collisions are handled by retransmitting frames after a random delay.

- random access protocol specifies:
  
  • how to detect collisions
  
  • how to recover from collisions (via delayed retransmissions)

- Examples of Protocols:
  
 - ALOHA and Slotted ALOHA.
   
 - CSMA (Carrier Sense Multiple Access).
   
 - CSMA/CD (Ethernet) and CSMA/CA (Wi-Fi).
   
#### Pure ALOHA (not with us)

- The first ALOHA protocol was unslotted and fully decentralized. 
- Nodes immediately transmit frames into the channel and waits for acknowledgement. 
- If ACK not received, node waits for random backoff time and re-sends data.
- If a collision occurs, nodes retransmit with a probability. Efficiency is less than slotted ALOHA. 
- If a first bit of a new frame overlaps with the last bit of a nearly finished frame, both frames will be completely destroyed and must be retransmitted later.

  <img src="https://www.myreadingroom.co.in/images/stories/docs/dcn/aloha%20Protocols_Pure%20aloha.JPG" width="60%" height="auto">

#### Slotted ALOHA (not with us)

- A simple random access protocol where time is divided into time-slots. 
- Nodes are allowed to transmit at slot beginnings. Collisions are detected, and nodes retransmit with a probability. 
- If a station misses out the allowed time, it must wait for the next slot. This reduces the probability of collisions.
- Efficiency is determined by the long-run fraction of successful slots.

  <img src="https://www.myreadingroom.co.in/images/stories/docs/dcn/aloha%20Protocols_Slotted%20aloha.JPG" width="60%" height="auto">

#### Carrier Sense Multiple Access (CSMA)
- CSMA protocols introduce `carrier sensing` to listen before transmitting and `collision detection` to stop transmitting if interference is detected. 

- A node listens to the channel before transmitting
  
  - If the channel is idle, the node transmits the entire frame.
    
  - If the channel is busy, the node waits until it becomes idle.

- This reduces collisions but does not eliminate them. The possibility of collision still exists because of propagation delay (2 may not hear each other’s just started transmission)
- collision: entire packet transmission time wasted distance & propagation delay play role in in determining collision probability
  
- Types of CSMA:(extra info) 
  - 1-Persistent CSMA: If station is busy, it will **CONTINUOUSLY** sense the medium and transmit the data again. [Probability=1]
  - Non-persistent CSMA: If station is busy, it will wait for a random time and **RANDOMLY** sense the medium before transmitting again.
  - p-Persistent CSMA: It applies to slotted channels; If station is idle, it it will **CONTINUOUSLY** sense and waits for the slotted time to transmits the data  [Probability=p]

#### CSMA/CD
- CSMA/CD is a variant of CSMA that introduces collision detection. It reduces the amount of time wasted in collisions (transmission aborted on collision detection)

- **Transmission Period**: The time required for a node to transmit a frame.
  
- **Idle Period**: The time required for a node to wait before transmitting again.
  
- **Contention Period**: The time required for a node to sense the medium before transmitting again.
  
- **Binary Exponential Backoff**: It uses binary exponential backoff algorithm to handle collisions.
  
- Nodes choose a random value from an increasing set for waiting after collisions.
  
- When a collision is detected, the transmitting nodes immediately stop transmitting and enter a backoff state.

#### CSMA/CA (extra)
- Similar to CSMA/CD, CSMA/CA begins with carrier sensing. Nodes listen to the channel to determine if it's busy or idle.
- CSMA/CA introduces virtual carrier sensing to estimate the channel's status. 
- CSMA/CA uses a contention window, which is a range of time during which a node may choose to transmit after sensing the channel is idle.
- The size of the contention window may vary, and it influences the probability of a successful transmission.

** Ethernet CSMA/CD Algorithm**
1. Frame Creation: The Ethernet NIC (Network Interface Card) receives the datagram from the network layer and creates a frame for transmission.
      
2. Carrier Sensing: The NIC senses the channel:
   
    - If idle, it starts transmitting the frame.
      
    - If busy, it waits until the channel becomes idle, then transmits.
   
3. Successful Transmission: If the entire frame is transmitted without a collision, the transmission is complete.
   
4. Collision Detection: If another transmission is detected during sending:
   
    -  Abort the transmission.
      
    -  Send a jam signal to inform other nodes of the collision.
          
5. Binary Exponential Backoff: After aborting the transmission due to a collision:
   
     -  Enter the backoff phase.  after mth collision, chooses K at random from {0,1,2, …, 2m-1}. Ethernet waits K·512 bit times, returns to Step 2(  Wait for a random delay before retrying, determined by the binary exponential backoff algorithm.)
       
     -   More collisions result in longer backoff intervals.

**Binary Exponential Backoff**

- Delay Selection
  
  - After each collision, the backoff interval is chosen randomly.
    
  - The interval increases with each collision.

- After the m-th collision, a random value **K** is chosen from the range:
  - $${0, 1, 2, …, \(2^m - 1\)}$$
- The NIC waits for \(K \times 512\) bit times before reattempting to transmit.

#### Examples
- **After the 2nd collision**: \(K\) is chosen from {0, 1, 2, 3}.
- **After the 3rd collision**: \(K\) is chosen from {0, 1, 2, …, 7}.
- **After 10 or more collisions**: \(K\) is chosen from {0, 1, 2, …, 1023}.

Channel Partitioning vs. Random Access MAC Protocols
| **Protocol Type**          | **Advantages**                                      | **Disadvantages**                                                                 |
|----------------------------|----------------------------------------------------|-----------------------------------------------------------------------------------|
| **Channel Partitioning**   | - Shares the channel efficiently and fairly at high load. | - Inefficient at low load:<br>  - Delay in channel access.<br>  - Only \( \frac{1}{N} \) bandwidth allocated, even if there's only one active node. |
| **Random Access**          | - Efficient at low load: A single node can fully utilize the channel. | - High load results in collision overhead.                                        |

### 6.3.3 Taking Turns Protocols (Controlled Access)

#### Reservation

- A station need to make a reservation before sending data.
- In each interval, a reservation frame precedes the data frames sent in that interval.
- If there are N stations in the system, there are exactly N reservation minislots in the reservation frame.
- When a station needs to send a data frame, it makes a reservation in its own minislot.
- The stations that have made reservations can send their data frames after the reservation frame. 

  <img src="https://lh3.googleusercontent.com/pw/ABLVV87UZqcqWAnjvjtJEv4aOyT7zCUGjsXN3IpnbYXROTCMHMKk0MYs_I4YY3UiacY0VLciUZX3UFWBZeQos1iVeAI13UpJi5mpykVtukwb8KrtujF3js3tJY1g-KeteIAkCjYeT6bwj8-N--2Av-TrdgCx=w1920-h422-s-no?authuser=1" width="60%" height="auto">

#### Polling

- The polling protocol requires one of the nodes to be designated as a Master node (Primary station).
- The master node polls each of the nodes in a round-robin fashion.
- The master node can determine when a node has finished sending its frames by observing the lack of a signal on the channel.
- `Functions`: `Poll` (receive) and `Select` (send) functions
- `Drawback`: Polling Delay; If master node fails, channel will be inoperative.

#### Token Passing

- The token passing protocol is similar to polling, but it uses a token to determine when a node has finished sending its frames.
- Decentralised and highly efficient; (No Master Node)
- A small, special-purpose frame known as a token is exchanged among the nodes in some fixed order.
- When a node receives a token, it holds onto the token only if it has some frames to transmit; otherwise, it immediately forwards the token to the next node.
- `Examples`: Physical, Dual, Star, a
If the calculated checksum mismatches the received checksum, an error is detected.
See more on: Checksums in UDP

**Polling vs. Token Passing**

| **Technique**     | **How It Works**                                                                                     | **Advantages**                                         | **Concerns**                                                                                  |
|--------------------|-----------------------------------------------------------------------------------------------------|-------------------------------------------------------|-----------------------------------------------------------------------------------------------|
| **Polling**        | - A centralized controller invites nodes to transmit data one at a time.<br>- Commonly used in systems with “dumb” devices (e.g., Bluetooth). | - High efficiency: Ensures no collisions or empty slots.<br>- Fairness: Every node gets a chance to transmit. | - **Polling Overhead**: Extra communication to invite nodes increases latency.<br>- **Latency**: Nodes must wait their turn, even if they have data ready to send.<br>- **Single Point of Failure**: If the centralized controller fails, the system stops functioning. |
| **Token Passing**  | - A control token circulates among the nodes sequentially.<br>- A node can transmit only when it holds the token.<br>- After finishing, the token is passed to the next node. | - High efficiency: No collisions or empty slots.<br>- Suitable for time-sensitive networks (e.g., ring topologies). | - **Token Overhead**: Managing and circulating the token adds overhead.<br>- **Latency**: Nodes must wait for the token, which can delay transmission.<br>- **Single Point of Failure**: Loss of the token disrupts communication until it’s regenerated. |


## 6.4 Switched Local Area Networks

### 6.4.1 Media Access Control (MAC) Addresses

- IP Address (32-bit): Used at the network layer(3) for forwarding packets across networks.
  
- MAC Address (48-bit): Used at the data link layer for forwarding frames within a local network.
  
  - Typically 6 bytes (48 bits) long (for most LANs) and expressed in hexadecimal notation.
    
  -  used “locally” to get frame from one interface to another physically-connected interface (same subnet, in IP-addressing sense)
    
  - MAC addresses have a flat structure and remain constant for an adapter, analogous to a social security number.
    
  - Originally designed to be permanent in NIC , but now changeable via software.
    
  - Managed by IEEE to ensure uniqueness; Companies purchase a chunk of address space (224 addresses) for manufacturing adapters.

- each interface on LAN :has a unique 48-bit MAC address and has a locally unique 32-bit IP address

  <img width="300" alt="Screenshot 2024-12-03 at 5 27 06 AM" src="https://github.com/user-attachments/assets/eccec4d1-022d-4a79-a4d3-32aa5c5b01b3">

### 6.4.2 Address Resolution Protocol (ARP)

  - ARP resolves IP addresses to MAC addresses (between Network and Link-Layer Addresses), given its IP. 
    
  - Hosts and routers maintain ARP tables mapping IP addresses to MAC addresses.
    
  - ARP query packet used to obtain MAC address for a given IP address on the same subnet.
    
  - Plug-and-play; ARP tables get built automatically.
    
  - ARP table:
    
    - Each node (host or router) maintains a table mapping IP addresses to MAC addresses.
      
    -  Also contains a time-to-live (TTL) value, which indicates when each mapping will be deleted from the table. Defines how long the mapping is valid ( 20 minutes). After TTL expires, the entry is removed from the ARP table. 

**1. Same Subnet** :Device A wants to send data to Device B, but A does not know B's MAC address.

<img width="350" alt="Screenshot 2024-12-03 at 5 54 02 AM" src="https://github.com/user-attachments/assets/dc245b00-7cf7-4efb-bb79-e72ad759a476">

Broadcast ARP Request: Request - Reply - Update Table

1. Device A broadcasts an ARP query: Includes B's IP address. Sets the destination MAC address to FF-FF-FF-FF-FF-FF (broadcast). All devices in the LAN receive the request.

2. Device B Responds: Device B, recognizing its IP address in the query, sends an ARP reply directly to A. The reply includes B's MAC address.

3. Update  ARP Table: Device A receives B's MAC address and stores it in its ARP table along with the TTL value. Future frames for B are sent directly using the MAC address.


**1. Different Subnet** :Device A wants to send data to Device B, but A does not know B's MAC address.

When a device (A) needs to send a datagram to another device (B) located in a different subnet, a router (R) serves as the intermediary. 
This process involves the IP layer (for addressing and routing) and the MAC layer (for frame transmission).
<img width="450" alt="Screenshot 2024-12-03 at 5 58 59 AM" src="https://github.com/user-attachments/assets/aee77377-329e-42a9-90f6-07a904c35782">

1. Device A to Router R
* A creates an IP datagram:
    * Source IP: A’s IP address (111.111.111.111).
    * Destination IP: B’s IP address (222.222.222.222).
* A encapsulates the datagram in a link-layer frame:
    * Source MAC: A’s MAC address (74-29-9C-E8-FF-55).
    * Destination MAC: R’s MAC address (E6-E9-00-17-BB-4B).
* Frame is transmitted to R.

2. Router R Processes the Datagram
* R receives the frame:
    * Strips the link-layer header to extract the IP datagram.
    * Examines the destination IP address (222.222.222.222).
* R determines the next-hop interface:
    * Finds B’s subnet (222.222.222.0/24) in its routing table.
    * Uses ARP to find B’s MAC address (49-BD-D2-C7-56-2A).
* R creates a new link-layer frame:
    * Source MAC: R’s MAC address (1A-23-F9-CD-06-9B).
    * Destination MAC: B’s MAC address (49-BD-D2-C7-56-2A).
* Frame is transmitted to B.

3. Device B Receives the Frame
* B receives the frame:
    * Strips the link-layer header to extract the IP datagram.
    * Passes the datagram to the IP layer for further processing.

**Sending a Datagram off the Subnet**

  - When a host on a subnet wants to send a datagram to another subnet, ARP is still used.
  - Router interfaces play a crucial role in forwarding the datagram.
  - The destination MAC address is the router interface's MAC address, not the final destination's MAC address.
  - ARP is used to obtain the MAC address of the first-hop router.
  - Router consults a forwarding table to determine the correct interface for forwarding the datagram.
  - ARP is used again to obtain the MAC address of the ultimate destination.

    <img src="https://lh3.googleusercontent.com/pw/ABLVV85ip3uwpFb-_CdsLD7jkOiO3qB8xndr_7sjDFGBl1Bc0LMva75FMgNGtshNuxoLYOVNC1QtRRlhdZS5VMZEwma62vSR_VMyF30dCzCORAaGmYF6Xqzp4pixg7nXi0klaFi1QfMvewAaR9duSc9X1jNU=w1098-h298-s-no?authuser=1" width="60%" height="auto">

### 6.4.3 Ethernet Frame Structure
-  Ethernet is the most widely used LAN technology.
  
- Key Features:
  - Simple and cost-effective.
  - High adaptability with speeds ranging from 10 Mbps to 400 Gbps.
  - Implementation through a single chip that supports multiple speeds (Broadcom BCM5761).

- Physical Topology
  
1. Bus Topology (Popular until the mid-1990s):
   
 - All nodes share the same collision domain.
   
 - Nodes can interfere with each other, causing collisions.
   
 - Switched Topology (Modern Standard):

2. A central switch connects all devices.
   
  - Each connection (or "spoke") runs a separate Ethernet protocol.
    
  - Eliminates collisions as each connection is isolated.

- Hybrid Topology combines bus and star designs.
  
<img width="400" alt="Screenshot 2024-12-03 at 6 08 31 AM" src="https://github.com/user-attachments/assets/45c49f12-6542-49f7-92d3-97df3e1959c1">

Collisions can occur in the bus topology part. The star topology prevents collisions due to the centralized switch.

An Ethernet frame consists of the following components:

<img width="472" alt="Screenshot 2024-12-03 at 6 09 58 AM" src="https://github.com/user-attachments/assets/456b13a3-f19f-449c-b98f-d8ecddec3c39">

1. **Data field (46 to 1,500 bytes):** Carries the IP datagram, with a maximum transmission unit (MTU) of 1,500 bytes.
   
2. **Destination address (6 bytes):** MAC address of the destination adapter.
   
3. **Source address (6 bytes):** MAC address of the transmitting adapter.
   
  - if adapter receives frame with matching destination address, or with broadcast address (e.g., ARP packet), it passes data in frame to network layer protocol. otherwise, adapter discards frame
    
4. **Type field (2 bytes):** Indicates the higher-layer protocol (IP, ARP).  Permits multiplexing of network-layer protocols.
    
5. **Cyclic redundancy check (CRC) (4 bytes):** cyclic redundancy check at receiver. error detected: frame is dropped

6. **Preamble (8 bytes):** Synchronizes clocks between adapters.
   - used to synchronize receiver, sender clock rates
   - 7 bytes of 10101010 followed by one byte of 10101011
   - used to “wake-up” receiver adapter and synchronize receiver, sender clock rates

**Characteristics of Ethernet** 

- **Connectionless**:
  - No handshaking between sender and receiver NICs.
- **Unreliable**:
  - No acknowledgments (ACKs) or negative acknowledgments (NAKs).
  - Dropped data is lost unless recovered by higher-layer protocols (e.g., TCP).
- **Access Protocol**:
  - Uses unslotted CSMA/CD with binary exponential backoff for collision handling.

**802.3 Ethernet Standards: Link & Physical Layers**

- Ethernet includes many standards, differing in speed and physical layer media while maintaining a common MAC protocol and frame format.
  
- common MAC protocol and frame format
  
- different speeds: 2 Mbps, ... 100 Mbps, 1Gbps, 10 Gbps, 40 Gbps, 80 Gbps
  
- different physical layer media: fiber, cable
  
<img width="400" alt="Screenshot 2024-12-03 at 6 16 45 AM" src="https://github.com/user-attachments/assets/7b2cc72c-a1af-4197-b4d7-60decf77ed77">


### 6.4.3 Link-Layer Switches
### **Ethernet Switch Overview**

#### **Role of a Switch**
- A **link-layer device** that actively forwards frames.
- Functions:
  - **Stores and forwards** Ethernet frames.
  - Examines frame's **MAC address** to determine the appropriate outgoing link(s).
  - Uses **CSMA/CD** to access segments when forwarding frames.

#### **Features**
1. **Transparent Operation**:
   - Hosts remain **unaware** of the switch's presence.
2. **Plug-and-Play, Self-Learning**:
   - Requires no manual configuration.

---

### **Multiple Simultaneous Transmissions with a Switch**

#### **Advantages**
- Hosts have **dedicated, direct connections** to the switch.
- Switches **buffer packets** for each link.
- Ethernet protocol operates independently on **each incoming link**:
  - **No collisions** due to full-duplex communication.
  - Each link forms its **own collision domain**.

#### **Switching Capability**
- Enables **simultaneous transmissions**, such as:
  - Example: A to A' **and** B to B'.
- **Limitation**:
  - Cannot process multiple transmissions involving the **same outgoing port** at the same time (e.g., A to A' **and** C to A').


- The primary role of a switch is to receive incoming link-layer frames and forward them to outgoing links. Switches employ buffers in output interfaces to handle potential rate mismatches.
- Switches perform two key functions: `filtering` and `forwarding`. 
- Filtering determines whether a frame should be dropped or forwarded, while forwarding identifies the interfaces to which a frame should be directed. 
- This is achieved through a switch table, containing MAC addresses, associated interfaces, and entry timestamps. Unlike routers, switches forward based on MAC addresses, not IP addresses.

When a frame arrives:
- If no entry exists for the destination address, the switch broadcasts the frame.
- If there's an entry for the destination on the same interface, the frame is discarded.
- If there's an entry for a different interface, the frame is forwarded to that interface.

**Advantages of Switches**
- `Elimination of Collisions`: Switches prevent collisions, maximizing bandwidth.
- `Heterogeneous Links`: Different LAN links can operate at varying speeds and media types.
- `Management`: Switches offer enhanced security and ease of network management.

**Self-Learning Capability**

Switches possess the self-learning property, automatically building and updating their switch tables without manual intervention. The process involves recording MAC addresses, associated interfaces, and timestamps for each incoming frame. Entries are removed after a specified aging time, ensuring the switch table remains accurate.

## 6.6 Virtual Local Area Networks (VLANs)

- Hosts within a VLAN communicate as if connected directly to the switch, creating broadcast domains within each VLAN. 
- VLANs effectively isolate traffic, optimize switch utilization, and simplify user management.

In a port-based VLAN,
- Switch ports are grouped into VLANs by the network manager.
- Each VLAN forms a broadcast domain, isolating broadcast traffic within the VLAN.

**VLAN Switch Configuration**

- The network manager configures port-to-VLAN mappings using switch management software. 
- Switch hardware delivers frames only between ports belonging to the same VLAN. 
- However, this introduces a challenge: How can traffic from one VLAN be sent to another?

**VLAN Trunking**

- To address inter-VLAN communication, VLAN trunking was introduced. A trunk port on each switch, configured to belong to all VLANs, facilitates VLAN interconnection. Trunk links forward frames between switches, ensuring effective communication.

- The IEEE standard 802.1Q defines VLAN trunking. A special Ethernet frame format includes a four-byte VLAN tag in the header, carrying the VLAN identity. The VLAN tag contains a Tag Protocol Identifier (TPID) field, Tag Control Information field with a VLAN identifier, and a priority field.

  <img src="https://lh3.googleusercontent.com/pw/ABLVV86GRpSCpPwDC_6jYwJMrRJN_qv1J9ibbzlXHiLnhqUDrzYnys_mi0UWgCl23AiOvD6pj5uSwmq2vfZCEwBnR6W4jHveYtPYUuApve3oEhqSQwx2EgrTUDipODFighaOcZSCm8vYj43Tc-rwjNgelxXX=w1070-h326-s-no?authuser=1" width="60%" height="auto">

## 6.7 Multiprotocol Label Switching (MPLS)

MPLS enhances IP router's forwarding speed by incorporating a key concept from virtual-circuit networks: `fixed-length labels`. Unlike circuit-switched networks, MPLS is a packet-switched, virtual-circuit network, co-existing with IP infrastructure.

- An MPLS-capable router adds a small MPLS header to a link-layer frame between MPLS-capable devices. This header includes fields like the `label`, `experimental bits`, an S bit indicating the end of a stacked header series, and a `time-to-live` field.
- MPLS-capable routers, known as `label-switched` routers, forward MPLS frames by looking up labels in their forwarding tables, eliminating the need to extract IP addresses for routing decisions.
- MPLS labels and their associations with IP destinations are distributed among MPLS-capable routers. 
- MPLS introduces traffic engineering capabilities, allowing routers to forward packets along paths not determined by standard IP routing protocols. This enables network operators to override normal IP routing for specific traffic management purposes.
- MPLS can be utilized for fast restoration of forwarding paths, implementing virtual private networks (VPNs), and various other purposes.





