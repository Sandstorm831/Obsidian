- In the wireless LAN space, IEEE 802.11 wireless LAN is most widely adopted across the globe
- Here is a simple overview of IEEE 802.11 LAN architecture ![[IEEE_802_11_LAN_architecture.excalidraw]]
	Here 
	- `BSS`: Basic Service Set, it contains 1 or more wireless stations and a central base station known as access point (AP)
	- `AP`: Access Point, connects to an interconnection device (Router/Switch)

- To connect to the `AP`, scanning of available wireless LAN need to be performed. There are two strategies to scan for available wireless LAN
	- passive scanning: `AP` send beacon frames periodically on one of the 11 channels. Wireless hosts constantly listen on all the 11 channels for these beacon frames. After choosing 1 AP to connect, the wireless host sends a association request frame to the selected AP and AP responds with the association response frame ![[LAN_association_passive_scanning.excalidraw]]
	- Active scanning: Wireless host broadcasts a probe frame that will be received by all `AP` within range. `AP` respond with a probe response frame. The wireless host can then choose the `AP` with which to associate from among the responding `AP` ![[LAN_association_active_scanning.excalidraw]]

- 802.11 MAC Protocol: Since 802.11 Wireless LAN can associate with multiple hosts at the same time and engage in frame transfer, there will be problem for multi-access. 802.11 uses `CSMA/CA` to Multi-Access Control. It stands for "carrier sensing multiple access / collision avoidance". We focus on "collision avoidance" instead of "collision detection" as collision detection because:
	- strength of signal received and sent are different making it costly to build hardware to detect collision
	- all collisions would not be detected due to hidden terminal problem and fading
	To describe 802.11 `CSMA/CA` protocol, suppose that a station (wireless host or `AP`) has a frame to transmit
	1. If initially station senses the channel idle, it transmits frame after a short period of time known as **Distributed Inter-frame Space (`DIFS`)**
	2. otherwise, stations chooses a random back-off value using binary exponentiation back-off and counts down this **after** `DIFS` **when** the channel is sensed idle. Counter is frozen when channel is busy
	3. When counter reaches 0 (it will only happen when the channel is sensed idle), the station transmits the entire frame and wait fro Acknowledgement
	4. If acknowledgement is received, station understand frame is received and if it have another frame to send it starts `CSMA/CA` protocol at step 2. If acknowledgement isn't received , the station reenters the back-off phase in step 2, with the random value chosen from a larger interval.![[802_11_CSMA_CA_protocol.excalidraw]]