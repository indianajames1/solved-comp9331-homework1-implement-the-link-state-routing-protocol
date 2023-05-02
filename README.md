Download Link: https://assignmentchef.com/product/solved-comp9331-homework1-implement-the-link-state-routing-protocol
<br>
For this assignment, your task is to implement the link state routing protocol. Your program will be running at all routers in the specified network. At each router, the input to your program is a set of directly attached routers (i.e. neighbours) and the costs of these links. Each router will broadcast link-state packets to all other routers in the network. Your routing program at each router should report the least-cost path and the associated cost to all other routers in the network. Your program should be able to deal with failed routers.

<h2>3.1 Learning Objectives</h2>

On completing this assignment, you will gain sufficient expertise in the following skills:

<ol>

 <li>Designing a routing protocol</li>

 <li>Link state (Dijkstra’s) algorithm</li>

 <li>UDP socket programming</li>

 <li>Handling routing dynamics</li>

</ol>

<h1>4.  Assignment Specifications</h1>

This section gives detailed specifications of the assignment.

<h2>4.1 Implementation Details</h2>

In this assignment, you will implement the link state routing protocol.

Your program should be named <strong>Lsr.py</strong> (or <strong>Lsr.java</strong> or <strong>Lsr.c</strong>). It will accept one command line argument

<ul>

 <li><em>CONFIG.TXT</em>, this file will contain the router ID and its port No in the first line. The second line has the number of neighbours for this router. Subsequent lines would have neighbour’s information including the Router ID, the cost to the neighbouring router and the port number being used by the neighbour for exchanging routing packets. An example of this file is provided below.</li>

</ul>

Since we can’t let you play with real network routers, the routing programs for all the routers in the simulated network will run on a single desktop machine. However, each instance of the routing protocol (corresponding to each router in the network) will be listening on a different port number. If your routing software executes correctly on a single desktop machine, it should also work correctly on real network routers. Note that, the terms router and node are used interchangeably in the rest of this specification.

Assume that the routing protocol is being instantiated for a router A, with two neighbours B and C. A simple example of how the routing program would be executed (assuming it is a Python program named Lsr.py) follows:

<em>python</em> <em>Lsr.py configA.txt </em>where configA.txt is the configuration file for Router A that has the following details:

<ul>

 <li>5000</li>

</ul>

2

<ul>

 <li>5 5001</li>

 <li>2 5002</li>

</ul>




The first line has the router ID for this router (A) and the port No 5000 for communication (transmitting and receiving link-state packets). The 2nd line of this file indicates the number of neighbours for Router A. Note that it is not the total number of routers in the network. Following this, there is one line dedicated to each neighbour. It starts with the neighbour ID, followed by the cost to reach this neighbour and finally the port number that this neighbour is using for communication. For example, the second line in the configA.txt above indicates that the cost to neighbour B is 6.5 and this neighbour is using port number 5001 for receiving and transmitting link-state packets. The router IDs will be uppercase alphabets and you can assume that there will be no more than 10 nodes in the test scenarios. However, do not make assumptions that the router IDs will necessarily start from the letter A or that they will always be in sequence. The link costs should be floating point numbers (up to the first decimal) and the port numbers should be integers. These three fields will be separated by a single white space between two successive fields in each line of the configuration file. The link costs will be static and will not change once initialised. Further, the link costs will be consistent in both directions, i.e., if the cost from A to B is 6.5, then the link from B to A will also have a cost of 6.5. You may assume that the configuration files used for marking will be consistent with the above description and devoid of any errors.

<strong>Important:</strong> It is worth re-stating that initially each router is only aware of the costs to its direct neighbours.

The routers do not have global knowledge (i.e. information about the entire network topology) at start-up.

The remainder of the specification is divided into two parts, beginning with the base specification as the first part and the subsequent part adding new functionality to the base specification. <strong><em>CSE</em></strong> students are to attempt both parts of the assignment while <strong><em>Non-CSE</em></strong> students are required to only attempt the base specifications. The marking guidelines are thus different for CSE and non-CSE students. These appear at the end of the specification indicating the distribution of marks.

<h1>Part 1: Base Specification</h1>

In link-state routing, each node broadcasts link-state packets to all other nodes in the network, with each link-state packet containing the identities of the node’s neighbours and the associated costs to reach them. You must implement a simple broadcasting mechanism in your program. Upon initialisation, each router creates a link-state packet (containing the appropriate information – see description of link-state protocol in the textbook) and sends this packet to all direct neighbours. The exact format of the link-state packets that you will use is left for you to decide. Upon receiving this link-state packet, each neighbouring router in turn broadcasts this packet to its own neighbours (excluding the router from which it received this linkstate packet in the first place). This simple flooding mechanism will ensure that each link-state packet is propagated through the entire network.

It is possible that some nodes may start earlier than their neighbours. As a result, a node might send the link-state packet to a neighbour, which has not run yet. You should not worry about this since the routing program at each node will repeatedly send the link-state packet to its neighbours and a slow-starting neighbour will eventually get the information. That said, when we test your assignment, we would ensure that all nodes are initiated simultaneously (using a script).

Each router should periodically broadcast the link-state packet to its neighbours every

UPDATE_INTERVAL. You should set this interval to 1 second. In other words, a router should broadcast a link state packet every second.

Real routing protocols use UDP for exchanging control packets. Hence, you MUST use UDP as the transport protocol for exchanging link-state packets amongst the neighbours. Note that, each router can consult its configuration file to determine the port numbers used by its neighbours for exchanging linkstate packets. Do not worry about the unreliable nature of UDP. Since, you are simulating multiple routers on a single machine, it is highly unlikely that link-state packets will be dropped. Furthermore, since linkstate packets are broadcast periodically, occasional packet loss will not impact the operation of your protocol. <strong>If you use TCP, a significant penalty will be assessed</strong>.

On receiving link-state packets from all other nodes, a router can build up a global view of the network topology. Given a view of the entire network topology, a router should run Dijkstra’s algorithm to compute least-cost paths to all other routers within the network. Each node should wait for a ROUTE_UPDATE_INTERVAL (the default value is 30 seconds) since start-up and then execute Dijkstra’s algorithm. Given that there will be no more than 10 nodes in the network and a periodic linkstate broadcast frequency of 1 second, 30 seconds is a sufficiently long duration for each node to discover the global view of the entire topology.

Once a router finishes running Dijkstra’s algorithm, it should print out to the terminal, the least- cost path to each destination node (excluding itself) along with the cost of this path. The following is an example output for node A in some arbitrary network:

I am Router A

Least cost path to router B: ACB and the cost is 14.2

Least cost path to router C: AC and the cost is 2.5




We will wait for duration of ROUTE_UPDATE_INTERVAL after running your program for the output to appear (some extra time will be added as a buffer). If the output does not appear within this time, you will be heavily penalised. As indicated earlier, we will restrict the size of the network to 10 nodes in the test topologies. The default value of 30 seconds is sufficiently long for all the nodes to receive link-state packets from every other node and compute the least-cost paths.

Your program should execute forever (as a loop). In other words, each node should keep broadcasting link-state packets every UPDATE_INTERVAL and Dijkstra’s algorithm should be executed and the output printed out every ROUTE_UPDATE_INTERVAL. You should be able to kill an instance of the routing protocol (e.g., type CTRL-C at the respective terminal).

<strong>Restricting Link-state Broadcasts:</strong> Note that, a naïve broadcast strategy; wherein each node retransmits every link state packet that it receives will result in unnecessary broadcasts and thus increase the overhead. To elaborate this issue, consider the example topology discussed in the latter part of the spec (Figure 1). The link-state packet created by node B will be sent to its direct neighbours A, C, D and E. Each of these three nodes will in turn broadcast this link-state packet to their neighbours. Let us consider Node C, which broadcasts B’s link state packet to D. Note that node D has already broadcast B’s link state packet once (when it received it directly from B). Node D has now received this same link-state packet via node C. There should thus be no need for node D to broadcast this packet again. You MUST implement a mechanism to reduce such unnecessary broadcasts. This can be achieved in several ways. You are open to choose any method to achieve this. You must describe your method in the written report.

<h1>Part 2: Dealing with Node Failures (For CSE students only)</h1>

In this part, you must implement additional functionality in your code to deal with random node failures. Recall that in the base assignment specification it is assumed that once all nodes are up and running they will continue to be operational till the end when all nodes are terminated simultaneously. In this part, you must ensure that your algorithm is robust to node failures. Once a node fails, its neighbours must quickly be able to detect this and the corresponding links to this failed node must be removed. Further, the routing protocol should converge and the failed nodes should be excluded from the least-cost path computations. The other nodes should no longer compute least-cost paths to the failed nodes. Furthermore, the failed nodes should not be included in the least-cost paths to other nodes.

A simple method that is often used to detect node failures is the use of periodic heartbeat (also often known as keep alive) messages. A heartbeat message is a short control message, which is periodically sent by a node to its directly connected neighbours. If a node does not receive a certain number of consecutive hearbeat messages from one of its neighbours, it can assume that this node has failed. Note that, each node transmits a link-state packet to its immediate neighbour every UPDATE_INTERVAL (1 second). Hence, this distance vector message could also double up as the hearbeat message. Alternately, you may wish to make use of an explicit heartbeat message (over UDP), which is transmitted more frequently (i.e. with a period less than 1 second) to expedite the detection of a failed node. It is recommended that you wait till at least 3 consequent heartbeat (or link-state) messages are not received from a neighbour before considering it to have failed. This will ensure that if at all a UDP packet is lost then it does not hamper the operation of your protocol.

Once a node has detected that one of its neighbours has failed, it should update its link-state packet accordingly to reflect the change in the local topology. Eventually, via the propagation of the updated linkstate packets, other nodes in the network will become aware that the failed node is unreachable and it will be excluded from the link-state computations (i.e. Dijkstra’s algorithm).

You need to consider the case when a failed router joins back the topology again and starts sending link state update messages. <strong>Note that we are not considering the case when a previously unknown node joins the topology.</strong>

While marking, we will only fail a few nodes, so that a reasonable <strong>connected</strong> topology is still maintained.

Furthermore, care will be taken to ensure that the network does not get partitioned. In a typical topology (recall that the largest topology used for testing will consist of 10 nodes), at most 3 nodes will fail. However, note that the nodes do not have to fail simultaneously.

Recall       that       each       node       will       execute       Dijkstra’s       algorithm       periodically       after

ROUTE_UPDATE_INTERVAL (30 seconds) to compute the least-cost path to every other destination. It may so happen that the updated link-state packets following a node failure may not have reached certain nodes in the network before this interval expires. As a result, these nodes will use the old topology information (prior to node failure) to compute the least-cost paths. Thus, the output at these nodes will be incorrect. This is not an error. It is just an artefact of the delay incurred in propagating the updated linkstate information. To account for this, it is necessary to wait for at least two consecutive ROUTE_UPDATE_INTERVAL periods (i.e. 1 minute) after the node failure is initiated. This will ensure that all the nodes are aware of the topology change. While marking, we will wait for 2*ROUTE_UPDATE_INTERVAL following a node failure before checking the output.

<h2> 4.1 Test Topology</h2>

You have been provided with configuration files for a sample topology of 6 routers. Use this topology to incrementally test your implementation. The correct output for Router A, before and after failure of Router D is given.




The numbers alongside the links indicate the link costs. The configuration files for the 6 nodes are available for download from the assignment webpage. In these configuration files, we have assumed the following port assignments: A at 5000, B at 5001, C at 5002, D at 5003, E at 5004 and F at 5005. However, note that while testing your implementation on a CSE server, some of these ports may be in use by another student logged on to the same CSE machine as you. In this case, change the port assignments in all the configuration files appropriately.

Following are the output for Router A before and after Router D has failed.

Output with all routers working:




I am Router A

Least cost path to router C:AFDC and the cost is 4.5

Least cost path to router B:AFDCB and the cost is 5.6

Least cost path to router E:AFDE and the cost is 5.8

Least cost path to router D:AFD and the cost is 2.9

Least cost path to router F:AF and the cost is 2.2







Output after Router D fails:




I am Router A

Least cost path to router C:ABC and the cost is 7.6

Least cost path to router B:AB and the cost is 6.5

Least cost path to router E:AFE and the cost is 8.4

Least cost path to router F:AF and the cost is 2.2

You are encouraged to post new topologies on WebCMS course discussion forum (config files) along with output for different routers so that other students can benefit from testing with different topologies.

Here is a useful link that can generate correct answers for various topologies.

<u>https://www.cs.usfca.edu/~galles/visualization/Dijkstra.html</u>

<h1>5. Additional Notes</h1>

<ul>

 <li>This is NOT a group assignment. You are expected to work on this individually.</li>

 <li><strong>Language and Platform</strong>: You are free to use C, JAVA or Python to implement this assignment. Please choose a language that you are comfortable with. <strong>The programs will be tested on CSE Linux machines. So please make sure that your entire application runs correctly on these machines (i.e. your lab computers). We are unable to mark your assignment if it does not compile or run correctly on CSE lab computers, resulting in loss of significant marks</strong>. This is especially important if you plan to develop and test the programs on your personal computers (which may possibly use a different OS or OS version or IDE). Note that CSE machines support the following: <strong>gcc version 4.9.2, </strong></li>

</ul>

<strong>Java </strong><strong><span style="text-decoration: line-through;">1.7</span> 11.0.3, Python 2.7, 2.8 and 3. If you are using Python, please clearly mention in your report which version of Python we should use to test your code. </strong>You may only use the basic socket programming APIs provided in your programming language of choice. You may not use any special ready-to-use libraries or APIs that implement certain functions of the specifications for you.




<ul>

 <li>Note that the configuration files supplied as an argument to each node will be consistent with the test topology. Your programs do not have to handle errors in format, etc.</li>

 <li>You should be aware that port ID’s, when bound to sockets, are system-wide values and thus other students may be using the port number you are trying to use. On Linux systems, you can run the command ‘netstat’ to see which port numbers are currently assigned.</li>

 <li>Do not worry about the reliability of UDP in your assignment. It is possible for packets to be dropped, for example, but the chances of problems occurring in a local area network are fairly small. If it does happen on the rare occasion, that is fine. Further, your routing protocol is inherently robust against occasional losses since the link state packets are exchanged every 1 second. If your program appears to be losing or corrupting packets on a regular basis, then there is likely a fault in your program.</li>

 <li>Test your assignment out with several different topologies (besides the sample test topology provided). Make sure that your program is robust to node failures by creating several failed nodes (however make sure that the topology is still connected).</li>

 <li>You are free to design your own format and data structure for the messages. Just make sure your program handles these messages appropriately.</li>

 <li>Consider using multi-threading in your implementation. We recommend that you use at least three separate threads: for listening (for receiving LSA’s), sending (for sending LSA’s after every 1 sec) and Dijkstra’s calculations (after every 30 sec). You may choose to use more than three threads as per your requirement.</li>

 <li>You are encouraged to use the course discussion forum to ask questions and to discuss different approaches to solve the problem. However, <strong>you should</strong> <strong>not post any code fragments on the forum</strong>.</li>

</ul>