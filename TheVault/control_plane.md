- Link State (LS) Routing algorithm (centralised algorithm)
	In this, every router have the data of all the nodes, links and cost of each of the link in the network, and using this information, it calculates the shortest distance from the current source node to every other node using `Dijkstra's algorithm`
	- `D(v)`: cost of the least-cost path from the source node to node `v` as of this iteration of the algorithtm
	- `p(v)`: previous node (neighbour of `v`) along the current least-cost path from source to node `v`
	- `N'`: subset of nodes, `v` is in `N'` if the least-cost path from source to v is definitively known
	- c(u,v): if v is neighbour of u, this value would be the link cost between them, otherwise it will be inf

	```
	Initialization
	N` = {u}
	for all nodes v:
		if v is neighbour of u:
			D(v) = c(u,v)
		else D(v) = inf
	
	Loop
	find w not in N` such that D(w) is minimum
	add w to N`
	update D(v) for each neighbour v of w and not in N`:
		D(v) = min(D(v), c(w,v) + D(w))
	untill N` = N
	```


- Distance Vector (DV) routing algorithm (decentralised algorithm)
	In this every router interacts with the routers to which it is directly connected with, if it receives some information, it performs some calculations over the information and distributes the results to it's neighbours, this process is continued until no information is received from anywhere. It uses `bellman-ford algorithm` $${d_x(y)} = min_v\{c(x,y) + d_v(y)\}$$
	to calculate the minimum distances to other nodes. 
	
	Each node store the following information:
	- for each neighbour v, the cost c(x,y) from x to directly attached neighbour, v
	- Node x's distance vector, `Dx`, $$D_{x} = [D_x(y): y\space in\space N]$$ containing x's estimate of cost to reach any of node y in N
	- distance vector of each of it's neighbours
	```
	Initialisation
	for all destinations y in N:
		if y is neighbour of x:
			D_x(y) = c(x,y)
		else:
			D_x(y) = inf
	for each neighbour w:
		D_w(y) = ? for all destinations y in N
	for each neighbour w:
		send distance vector D_x
	
	Loop
	wait   (untill I receive a distance vector from any of my neighbour w
			or
			untill I see a link cost change to some neighbour w)
		for each y in N:
			D_x(y) = min_v(D_v(y) + c(x,v))
	if D_x(y) changed for any destination y:
		send distance vector D_x
	```
	Remember that is faces the problem of `count-to-infinity` problem


- autonomous systems (`ASs`): a subset of routers that are under one administrative control. 
- Open Short Path First (`OSPF`): used for `intra-AS` routing, it's a link state protocol. It's primarily used as `intra-ISP` routing protocol
	- Security: only trusted routers can participate in `OSPF` protocol in an AS, it uses
		- simple: passwords are configured in each router, and passwords are sent in plain text, not very secure
		- MD5: packet's hash is calculated along with the password/secret using MD5 and sent, receiving router recalculates the hash and matched to verifying authenticity
	- Hierarchy Support: Each `OSPF` autonomous system can be subdivided in areas where each area runs it's own `OSPF`. Exactly one area is configured as `Backbone area` which is responsible for routing packets between different areas. It contains each area's border routers. So `inter-area` routing would look like:
	 `intra-Area` routing to border routers -> `intra-Area` routing inside Backbone area to the border router of destination area -> `intra-Area` routing to final destination router from border router

- Border Gateway Protocol (`BGP`): used for `inter-AS` routing, it's a DB protocol. It's primarily used as `inter-ISP` routing protocol. It fulfils primarily two tasks:
	- Obtain `prefix` reachability info from neighbouring `AS`: primarily accomplished by the `subnet` screaming `I exist and I am here` and `BGP` makes sure that all routers know about this `subnet`
	- Determine the best route to `prefixes`: There can be more than a dozen paths to reach that `subnet`, `BGP` runs a route-selection algorithm and gives the best path
	![[Network_BGP_router_advertising.excalidraw]]
	![[Network_BGP_hot_potato_routing.excalidraw]]
	
	best route decision is based on the following algorithm:
	1. a route is assigned a `local preference` value and it's an admin's decision. Routes with highest preference numbers are selected
	2. from the remaining paths (have same highest preference number), one with the shortest AS-PATH are chosen
	3. From remaining, hot-potato routing is done
	4. if still some routes remains, router uses some BGP identifiers to select the route


- BGP also used in `DNS resolution` using `IP-Anycast`