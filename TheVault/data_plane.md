- Router
	![[Network_Router.excalidraw]]
	- Input Port
	![[Network_router_input_port.excalidraw]]
	- Output Port
	![[Network_router_output_port.excalidraw]]
	- Each `interface` has it's own `IP` whereas, `interface` is where a host and physical link meet, thus a router can have multiple `ips`
- IPv4
	- `32 bits` or `4 bytes` long : `xxx.xxx.xxx.xxx` dotted decimal notation
	- `CIDR`: classless interdomain routing : generalises subnet-addressing : `a.b.c.d/x` this is `subnet address` where  `/x` is the `subnet mask`: it represents the `most significant bits` of `a.b.c.d` IPv4 address
	![[Network_ipv4_subnet.excalidraw]]
	- `Dynamic Host Configuration Protocol (DHCP)` : used to allocate `ip` dynamically in a network
	![[Network_DHCP_Protocol.excalidraw]]
	- `Network Address Translation (NAT)`: A `router` which connects a `LAN` to a `WAN/Internet` can be made `NAT Router`, it essentially does the following things
		- For `internet/WAN`, the router act as a single `host` with a single `ip` and all the requests are being generated from it. Thus, all requests from the `LAN` to the `Internet` will have the same `ip`, that is of the router
		- `NAT router` forwards request from the `LAN` to the `internet` with it's own `ip` and `port`, and the same port is used to map to the host (`ip + port`) in the `NAT Table`.
		- `NAT Table`, keeps the map of hosts (`ips+port`) from within `LAN` with a `port` number on router when a request come from `LAN`
		- When a request is received at a `port` at `NAT Router` from `Internet`, it checks if an entry exists for that port in the `NAT Table`, if yes, then it forward the result to the relevant `host`
- IPv6: 
	- `128 bits` long
	- 40 bytes header