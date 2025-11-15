link layer is primarily responsible to move packets from 1 node to another. So there can 2 cases here
1. point-to-point link: single node to single node transfer, nothing fancy, no link layer protocol is really needed
2. multi-access link: link shared by several nodes, thus high chance of signal interference and other stuff. Link-layer protocol is needed.

Multi-Access Protocols: 
- Channel Partitioning Protocol:
	- Time Division Multiplexing (TDM): each node is assigned a slot only the transmission is possible
	- Frequency Division Multiplexing (FDM): each node is assigned a frequency band for transmission
	- Code Division Multiple Access (CDMA): allows simultaneous transmission due to assigning of different `codes` to different nodes. detail in [[CDMA]]
- Random-Access Protocol: 
	- slotted ALOHA: 
		- time is divided into slots: slot size = L/R (frame_size/transmission_rate)
		- transmission happen only at starting of slot
		- collision is detected before the ending of slot to all nodes
		- clock of nodes are synchronised
		- on receiving a frame, wait for starting of next slot and transmit
		- if collision is detected, node will re-transmit the frame in each subsequent slot with a probability of `p` until the frame is transmitted successfully
		- frame will be transmitted successfully only when only one node is transmitting at that time. average efficiency is 37%
	- Carrier Sensing Multiple Access / Collision Detection (CSMA/CD) protocol
		- carrier sensing: if the multi-access link is currently being used by some other node, wait until it's empty, and then transmit
		- collision detection: if a collision is detected while transmission, stop transmission.
		- If collision is detected, then wait for a random amount of time before transmitting again.
		- random time is chosen by `binary exponential backoff`, if the node experienced `n` collisions, then a value `k` is selected from the set `{0,1,....,2^n -1}`, and the node will wait for a time value directly proportional to `k`
- Taking Turns Protocol
	- Polling protocol:
		- one node is designated as master node, polls all other nodes in a round-robin fashion
		- when master node polls node 1, node 1 then transmits max allowed nodes and similar for node 2 and so on
	- Toke passing protocol:
		- a token frame is circulated in a fixed fashion. nodes are allowed to transmit when they have the token
		- if node has frames to transmit then it transmit the max allowed frames and then passes the token, otherwise node passes the token immediately.

- MAC Addresses
	- 6 bytes long
	- every network interface have a MAC address
	- fixed address
	- FF-FF-FF-FF-FF-FF is the broadcast address

- Address Resolution Protocol (ARP)
	link layer attaches the MAC address of the destination host to the link-layer frame. For getting the MAC address, it looks in it's ARP table where the mapping of IP Addresses and MAC addresses are present with a TTL (time-to-live) field. ARP is basically used for finding MAC Addresses on the same subnet if an entry is not present in the ARP Table. working:
	- ARP module is given the IP whose MAC address is required.
	- sends a broadcast frame with sender and receiver IP and MAC address. receiver MAC is set as broadcast MAC
	- Every host checks if IP matches with it's own IP, if yes, it reverts with frame it's MAC address to the host from the whom the request came in.
	- If IP is not present in the subnet, then the MAC address is set to the router's MAC address which is connected to the internet or other sub-nets

- Ethernet Protocol
	- Most popular link layer protocol
	- popularly used in LAN with star-based topology with a switch at the centre
	- use CRC (cyclic redundancy check) for error detection
	- use CSMA/CD protocol for multi-access problem
	- fully-duplex (both incoming and outgoing at same time)

- Ethernet switches
	Uses filtering and forwarding for packets arriving. It has a switch table which is mapping of various mac-addresses with corresponding outgoing interfaces and time to expiry. It works as follows:
	- packet arrives, the MAC address to which the packet is addressed is not present in the table, switch transmits the packets to all outgoing links except the one from which the packet arrived
	- packet arrives, MAC address found in table, and the interface corresponding to the MAC address is the same from which packet came. Switch does nothing
	- packet arrives, MAC address is found in table, and interface is different from which the packet came. Switch transmit the packet to that interface.
	- Switch also stores the MAC addresses of the packet arriving with the interface from which they are arriving in the table constantly

Virtual Local Area Networks (VLANs)
- Nothing fancy, various interfaces of a Switch are grouped together in separate groups and and are called VLAN.
- VLAN Trunking is a used to connect switches which have VLAN groups.
- You can read more

Data Centre Networking
- TOR switches: Top of Rack switches, connects all the servers in a rack
![[Network_link_layer_data_center_network.png]]
- Highly Interconnected data centre network topology
![[Network_link_layer_data_center_highly_interconnected_network.png]]
