Intro:
Tcpdump is the command line network packet analyzer tool. It allows the user to display TCP/IP and other packets being transmitted or received over a network
to which the computer is attached.

Here’s why you should consider using tcpdump:

  Comprehensive Packet Capture: Capture and inspect packets on any network interface to troubleshoot connectivity issues, analyze network traffic, and monitor
  performance.
  Flexible Filtering: Use powerful filter expressions to focus on specific traffic, such as HTTP requests, DNS queries, or traffic from a particular IP address.
  Readable Outputs: Get detailed and human-readable outputs or delve into raw packet data for in-depth analysis.
  Capture & Save: Save captures to a file for later analysis with tools like Wireshark or read from files to review past traffic.

How tcpdump captures the packets:
  Pcap is the API used for capturing the network traffic which is originally written in C. On unix like systems tcpdump uses libpcap library to get the packets
  from network driver before they are processed by the OS.
  Similarly on Windows systems there is a port of libpcap named Winpcap which no longer developed and supported a is it is replaced by Npcap for Windows 7 and
  later that is still supported.
  packet capture works by creating copies of some or all packets passing through a given point in the network.

Breakdown of the tcpdump command:
  sudo tcpdump -i eth0 -nn -s0 -v port 8
  i => Analyze the traffic for eth0 interface. We can use any option with -i to analyze the packets on all network interfaces
  -nn => By default, it resolves IP addresses and port numbers into names. The single -n will not resolve the hostnames and -nn will not resolve both hostnames an
  ports. This is used to for not viewing the IP/port numbers. Also it will not slow down the capture when capturing the large amount of data as generally nam
  resolution will slow down the capture
  -s0 => It used to define the snap length or size of the capture in bytes. -s0 will set the size limit to unlimited. We could only use if we want to capture everything
  -v => Verbose. Increases the amount of detail shown in the output
  port 80 => This is port which captures the traffic on port 80 i.e on http

Reading the TCPDUMP output:
  Below is the example TCP output and the breakdown of the output.

  16:20:53.033159 IP 192.165.0.190.50382 > 33.290.265.101.443: Flags [P.], seq 1801899275:1801899299, ack 611021509, win 2048, options [nop,nop,TS val 437445718 ecr 2044200782], length 24

  16:20:53.033159 => This is the Timestamp in hh:mm:ss:fraction format.
  IP => It indicated it is 	IP protocol
  192.165.0.190.50382 => Source address and Source port number.
  33.290.265.101.443 => Destination address and dest port number
  Flags [P.] => This showa the TCP flags. P indicates Push, S indicates SYN and '.' indicates ACK.
  seq 1801899275:1801899299 => the TCP packet’s sequence number
  ack 611021509 => the TCP packet’s acknowledgement number
  win 2048 => Source host's window size
  options [nop,nop,TS val 437445718 ecr 2044200782] => TCP options. nop means there is no option set. TS val means TS value is a TCP timestamp; it helps determine
  the order that packets were sent. ecr indicates Echo reply which is sent with the acknowledgement field. Its a timestamp value calculated based upon the TSval sent in the sync packet
  length 24 => Packets length

TCPDUMP useful options:
  As of now we know how TCPDUMP works and how to read the output of the tcpdump. Now let see some of the basic options we can use with tcpdump command.

  -D or --list-interfaces => It will list down all the network interfaces
  -A => It will print each packet is an ASCII format.
  -c X or --count X => It will stop the capturing the packets after receiving the X number of Packets.
  -e => to get the ethernet header information.
  -w file-name => This option is used to capture the packet and save in the file.
  -r file-name => After saving the file if you want read the file then we use -r option along with file name.
  -v or -vv or -vvv => It will display the output in verbose format. if you use -vv and -vvv it will display packet information verbosely.
  -i => It is useful when you want the capture the packets for a specific interface. We can use any as an argument to capture a packets for all interfaces.
  -XX => It is used to limit the size of the captured packets.
  -s => It is used to define the snaplength (size) of the capture in bytes. We can use -s0 to get everything.
  -S => It will print the absolute sequence numbers.

These are some common options we can use with tcpdump command to get the appropriate details of network packets. Also we can Filter it by Protocol.

  tcpdump tcp => filter by tcp protocol
  tcpdump udp => filter by udp protocol
  tcpdump icmp => filter by icmp protocol
  tcpdump port 80 => filter by ports such as 80, 443 , 53 etc.
  tcpdump portrange 8000-9000 => filter by the portrange

  Filter by Source or Destination IP
  tcpdump src 192.168.1.1 => filter by source IP
  tcpdump dst 192.168.1.1 => filter by destination IP
  tcpdump host 192.168.1.1 and port 80 => Filter by dest IP and port
