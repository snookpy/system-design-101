
# Quorum
Quorum is a concept used in distributed systems to ensure consistency and reliability. It refers to the minimum number of nodes or replicas that must agree on a decision before it can be considered valid. Quorum is often used in consensus algorithms, such as Paxos and Raft, to ensure that a majority of nodes agree on a value before it is committed.

Mostly (N/2)+1 nodes are required to form a quorum in a distributed system with N nodes.

Quorum is achieved when nodes follow the below protocol: R + W > N, where:
 - N = nodes in the quorum group
 - W = minimum write nodes
 - R = minimum read nodes