Packet switching and Delays in Computer Network:

Introduction:
  In network applications two or more end systems exchange the messages with each other. It can be anything in messages such as an email message, JPEG image
  or any sort of data. To send this messages from source system to destination systems the source breaks the long message into small segment of data known as
  Packets. And these Packets travels through the communication links and packet switches(Router or link layer switches). At the destination all these small packets
  gets reassembled.
  Below is the simple diagram which shows the how Packets travels.
      Sender                     Network                          Receiver
    +----------+              +--------------+                 +-----------+
    |          |  Packet      |              |    Packet       |           |
    |   Host   | -----------> |   Router     | --------------> |   Host    |
    |          |              |              |                 |           |
    +----------+              +--------------+                 +-----------+

  So Packet Switching is the routing and transferring the data in the form of Packets over the various networks. The packets can take different network paths to the same destination,
  so long as they all arrive at the destination.
  Packets coming from different sources or senders can be mixed together and pulled apart later, which is called multiplexing.
  Advantage Over Context Switching: the net- work can be more resilient and there can be better utilization of the network links and switches because of statistical multiplexing.

  Most of the Packet Switch uses the Store and Forward technique while switching the packets. Store and Forward transmission means that the packet switch must
  receive the entire packet before it can start to transmit the first bit of the packet onto the outbound link.

Processing Delay:
  The time require to examine the packets headers and to determine where to direct the Packet is a part of Processing Delay.
  Processing delay is the CPU cycles needed to look at the packet headers and decide what to do with the packet, and do it – basically the time needed to process the packet
  During processing of a packet, routers may check for bit-level errors in the packet that occurred during transmission as well as determining where the packet's next destination is.
  the processing delay can be quite large especially where routers are performing complex encryption algorithms and examining or modifying packet content

Queuing Delay:
  Queueing delay is the time packet waits in a queue until earlier arriving packets transmitted on the link first.
  This term is most often used in reference to routers. When packets arrive at a router, they have to be processed and transmitted. A router can only process one packet at a time.
  As a queue begins to fill up due to traffic arriving faster than it can be processed, the amount of delay a packet experiences going through the queue increases.
  The speed at which the contents of a queue can be processed is a function of the transmission rate of the facility.

Transmission Delay:
  Transmission Delay is the amount of time required to push all the bits of packet into the wire. In other words, this is the delay caused by the data-rate of the link.
  Assuming that Packets are transmitted in a first-come-first-served manner, as this is common in Packet Switched network, out Packet can be transmitted only after
  all the packets that have arrived before it have been transmitted.
  It is a function of Packets length, it has nothing to do with the distance between two hosts. Below formula we can use to calcualte the deply.
          Dt = L/R
          Where
          Dt => is the transmission delay in seconds;
          L =>  is the number of bits or Packet length;
          R =>  is the rate of transmission (say, in bits per second).

Propogation Delay:
  The time require to propogate from the beginning of the link to router B is the Propogation Delay.
  The Propogation Delay is the distance between two router divided by the propogation speed.

Media Packetization Delay:
  This is also one of the important deplay which is present in Voice-over-IP applications. In VOIP the sending side must first fill the packet with encoded
  digitized speech before passing to the internet. This time to fill a packet called the Packetization Delay.
