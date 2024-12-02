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




