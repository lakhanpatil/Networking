Introduction:
Reliable Data Transfer (RDT) protocols are designed to ensure that distributed entities can communicate reliably over an inherently unreliable channel that might lose, corrupt, or reorder messages. The ultimate goal is to guarantee that data transferred from an upper layer arrives with no corrupted or lost bits and is delivered in the exact sequence it was sent. While the application layer may only need to send data in one direction (unidirectional), the underlying RDT protocol requires bidirectional communication because the sender and receiver must continuously exchange control packets back and forth to verify successful delivery.

To map out these interactions without misinterpretation, RDT relies on Finite State Machines (FSMs) to define the specific states, events, and actions of the sender and receiver. The protocol’s evolution is modeled by starting with a perfect channel and incrementally introducing more realistic network flaws.

Principle Of Reliable Data Transfer Protocol:
The principles of reliable data transfer revolve around building mechanisms that guarantee data is delivered accurately and in order, even when the underlying transport layer (such as the Internet Protocol) is unreliable and prone to losing, corrupting, or reordering packets
To build a reliable channel, several fundamental protocol mechanisms are utilized:

- Bidirectional Control Flow: Even if the application only requires unidirectional data transfer, the underlying sender and receiver must continuously exchange control packets back and
  forth. Because the sender and receiver do not automatically know what is happening on the other side—as if a curtain is drawn between them—they rely entirely on these communication signals
- Finite State Machines (FSMs): Protocols are formally mapped out using FSMs, which define the distinct states (e.g., waiting for data to send, waiting for a reply), events (e.g., a
  packet arrives, a packet is corrupted), and actions (e.g., sending an acknowledgment, retransmitting a packet) for both the sender and receiver
- Error Detection: Mechanisms such as checksums are attached to packets so the receiver can detect if any bits were flipped or corrupted in transit
- Receiver Feedback: The protocol relies on explicit feedback from the receiver. Positive Acknowledgments (ACKs) confirm that a packet was received cleanly, while Negative
  Acknowledgments (NAKs) flag that a packet arrived corrupted
- Retransmission: If a packet is flagged as corrupted via a NAK, the sender will automatically retransmit that packet. This is typically managed via a "stop-and-wait" protocol, where the
  sender transmits one packet and pauses to wait for the receiver's feedback before continuing.
- Sequence Numbers: A critical vulnerability occurs if an ACK or NAK gets garbled in transit, leaving the sender unsure if the receiver got the data. If the sender simply retransmits in
  this scenario, the receiver might end up with duplicate copies of the same data. To solve this, sequence numbers (such as alternating between 0 and 1) are added to packets so the receiver can easily spot and discard duplicate retransmissions

RDT 1.0: The Perfect Channel In this foundational model, the underlying channel is assumed to be completely perfect, meaning no segments will ever be lost, corrupted, duplicated, or reordered.
- Because the channel is flawless, the protocol is incredibly simple.
- The sender simply packages the data and sends it into the channel, while the receiver reads it, extracts the data, and passes it up to the application layer

RDT 2.0: Handling Bit Errors This iteration introduces a channel that might introduce bit errors, essentially flipping bits and making messages unintelligible at the receiver. To handle this, RDT 2.0 operates using a stop-and-wait behavior, where the sender transmits a single packet and pauses to wait for explicit feedback from the receiver. It introduces several key recovery mechanisms:
- Error Detection: Checksums are used so the receiver can detect if any bits were flipped.
- Receiver Feedback: The receiver sends Positive Acknowledgments (ACKs) to confirm a packet was received successfully, and Negative Acknowledgments (NAKs) to flag that a packet was corrupted.
- Retransmissions: If the sender receives a NAK, its programmed action is to automatically retransmit the garbled packet.
- The Fatal Flaw: RDT 2.0 has a critical oversight: it does not account for the ACK or NAK itself getting corrupted in transit. If the sender receives garbled feedback, it doesn't know if the receiver successfully got the message. If the sender blindly retransmits the data, the receiver might process a duplicate copy of the data, wrongly treating it as a brand-new message

RDT 2.1: Handling Corrupted Feedback To fix the vulnerability of garbled ACKs and NAKs, RDT 2.1 adds a 1-bit sequence number (alternating between 0 and 1) to each packet.
- If the sender receives a corrupted ACK or NAK, it simply retransmits the last packet using the same sequence number.
- When the receiver gets the packet, it checks the sequence number. This allows the receiver to easily detect and discard duplicate retransmissions without accidentally delivering a second copy of the data to the application layer.
- Because the protocol now has to track whether it is sending or waiting for a "0" packet or a "1" packet, the sender's finite state machine expands from two states to four

RDT 2.2: It is a version of the reliable data transfer protocol designed to operate entirely without Negative Acknowledgments (NAKs). While its predecessor, RDT 2.1, relied on both positive acknowledgments (ACKs) and NAKs to tell the sender if a packet was received cleanly or corrupted, RDT 2.2 is a "NAK-free" protocol that exclusively uses ACKs
- No New Mechanisms: It does not introduce any fundamentally new protocol mechanisms beyond what was established in RDT 2.1
- Reusing Existing Tools: To successfully function without NAKs, RDT 2.2 simply uses existing mechanisms—specifically sequence numbers, error detection (checksums), and retransmissions—in a slightly different way
- Foundation for TCP: The concepts in RDT 2.2 parallel real-world protocols like TCP, which similarly achieves reliable data transfer using only ACKs, sequence numbers, checksums, and retransmissions rather than relying on NAKs
- To handle a corrupted packet without a NAK, a receiver in RDT 2.2 sends an ACK for the last correctly received packet, explicitly including that packet's sequence number. When the sender receives a duplicate ACK for a sequence number it has already processed, it infers that the receiver got a corrupted packet and automatically retransmits the current packet.

Data packet flow in a reliable data transfer protocol:
The data packet flow in a reliable data transfer protocol involves a structured sequence of steps that safely move data from a sender to a receiver, even over an unreliable network channel. The flow operates through the following steps:

- Step 1: Data Initialization (Sender Side) The process begins when the sending application layer process wants to send a message and passes the data down to the transport layer. This
  action is initiated by calling the rdt_send() function
- Step 2: Packet Creation Once the transport layer receives the data, it breaks the message into smaller segments and adds a header to each segment to create a formal packet
- Step 3: Transmission The sender then transmits this newly created packet into the underlying unreliable channel by calling the udt_send() function
- Step 4: Packet Reception (Receiver Side) On the receiving side of the channel, the arrival of a packet triggers the rdt_rcv() function
- Step 5: Processing and Delivery If the packet arrives intact and uncorrupted, the receiving side removes the transport layer header to extract the original data. The protocol then
  calls the deliver_data() function to pass this extracted data up to the receiving application layer process. The ultimate goal of this delivery is to ensure that the data is provided to the receiving application exactly once and in the correct sequential order
- Step 6: Bidirectional Control Feedback Even though the actual application data is only flowing in one direction (from sender to receiver), the protocol must transmit control packets in
  both directions to ensure reliability. After processing the packet, the receiver uses udt_send() to send an Acknowledgement (ACK) back to the sender if the packet was received cleanly, or a Negative Acknowledgement (NAK) if the packet was corrupted. If the sender receives a NAK, its programmed reaction is to retransmit the packet

Data packet flow in a reliable data transfer protocol when there is any error or corrupt data:
When a reliable data transfer (RDT) protocol encounters an error or corrupted data over an unreliable channel, the packet flow relies on a "stop-and-wait" behavior and bidirectional control messages to recover the lost information.
Here is the step-by-step packet flow when a packet is corrupted in transit:
- Step 1: Transmission and Waiting The sender receives data from the application layer via the rdt_send() function, creates a packet with a header, and transmits it into the unreliable channel using the udt_send() function. Crucially, the sender then pauses and enters a state where it waits to receive feedback from the receive
- Step 2: Error Detection at the Receiver The packet arrives at the receiving side, triggering the rdt_rcv() function. The receiver examines the packet using error detection bits, such as a checksum, and determines that bits have been flipped and the data is corrupted
- Step 3: Negative Acknowledgement (NAK) Because the packet is corrupted, the receiver does not extract the data. Instead, it generates a Negative Acknowledgement (NAK) packet and sends it back to the sender to explicitly signal that the message arrived with errors
- Step 4: Retransmission The sender, which has been waiting for feedback, receives the NAK. In response, its programmed action is to automatically retransmit the exact same packet to the receiver
- Step 5: Successful Delivery The retransmitted packet arrives at the receiver. Assuming it is uncorrupted this time, the receiver extracts the data and uses the deliver_data() function to pass it up to the application layer. It then sends a Positive Acknowledgement (ACK) back to the sender. Upon receiving the ACK, the sender stops waiting and returns to its initial state to accept new data from its application layer

Handling Corrupted Feedback (The RDT 2.1 Scenario) The flow becomes slightly more complex if the data packet arrives safely, but the returning ACK or NAK itself gets corrupted on its way back to the sender.
If the sender receives garbled feedback, it cannot know if the receiver successfully got the message. Its default action is to safely retransmit the packet. However, to prevent the receiver from accidentally passing a duplicate copy of the data to the application layer, the protocol attaches a 1-bit sequence number (0 or 1) to each packet. When the receiver gets the retransmission, it checks the sequence number, recognizes it as a duplicate, and discards the packet without delivering the data upwards, while still sending an ACK back to the sender to clear the sender's waiting state
